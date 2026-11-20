---
title: "[기술 회고] 16바이트(128-bit) 렌더 정렬 키 최적화: 왜 기수 정렬(Radix Sort) 대신 Branchless SIMD std::sort를 선택했는가?"
permalink: /myendfield/simd-sortkey-vs-radix-sort/
excerpt: "60,000개의 드로우 패킷을 128비트 정렬 키로 정렬할 때 발생하는 기수 정렬의 16패스 메모리 복사 병목을 분석하고 SSE2 movemask 기반 분기 없는(Branchless) 비교자를 설계해 정렬 시간을 3.8배 단축한 과정입니다"
layout: single
categories:
  - Portfolio
  - Optimization
tags:
  - C++
  - SIMD
  - SSE2
  - Algorithm
  - Cache-Locality
  - Vulkan
  - Rendering-Pipeline
toc: true
toc_sticky: true
toc_label: "128비트 정렬 키 최적화 회고"
toc_icon: "bolt"
date: 2026-11-10
last_modified_at: 2026-11-10
mermaid: true
mathjax: true
---

> **[주요 3줄 (Summary)]**
> * **문제 상황:** 고밀도 캐릭터 10,000개(서브메시 19만 개, 컬링 후 가시 패킷 약 60,000개)의 파이프라인 상태 리바인딩을 최소화하기 위해 **16바이트(128-bit) 정렬 키**를 도입했으나 키 크기 확장으로 인해 매 프레임 정렬 단계에서 CPU 병목이 발생했습니다.
> * **의사결정:** 이론적 복잡도가 O(k · N)인 기수 정렬(Radix Sort)이 16바이트 키 환경에서는 **16패스 핑퐁 복사와 캐시 미스(Scatter Write)**로 인해 급격히 느려짐을 확인하고 제자리 정렬(`std::sort`)에 **SSE2 레지스터와 `movemask`를 활용한 분기 없는(Branchless) 비교 함수**를 주입하는 구조를 채택했습니다.
> * **최종 성과:** Ryzen 7 7700 단일 스레드(60,000개 패킷) 기준 정렬 소요 시간을 기수 정렬 대비 **73.7% 단축(3.92ms -> 1.03ms)**하고 상태 리바인드 호출을 프레임당 **200회 미만**으로 압축했습니다.
{: .notice--primary}

---
{: style="clear: none;"}

## 1. 문제 정의: 왜 64비트가 아닌 16바이트(128-bit) 정렬 키인가?

Vulkan과 같은 저수준 그래픽스 API에서 수만 개의 오브젝트를 렌더링할 때 가장 큰 CPU 병목은 드로우 콜(`vkCmdDrawIndexed`) 자체가 아니라 드로우 사이에 발생하는 **파이프라인 상태 객체(PSO) 변경**과 **디스크립터 셋(Descriptor Set) 리바인딩**입니다.

`Endfield-Vulkan-Renderer` 프로젝트에서는 서브메시 19개로 구성된 고밀도 캐릭터(레바테인.FBX) 10,000개를 동시에 구동합니다. 프러스텀 및 오클루전 컬링을 거친 뒤에도 매 프레임 약 **40,000~60,000개의 서브메시 드로우 패킷**이 살아남는데 이를 별도의 그룹화(Clustering) 로직 없이 **한 번의 정렬만으로 그리기 순서와 상태 묶기를 동시에 해결**하기 위해 변경 비용이 비싼 순서대로 구성된 **128비트(16바이트) 정렬 키**를 설계했습니다.

```
[ 128-Bit (16-Byte) Sort Key Bit Layout — 높은 비트일수록 상태 변경 비용이 비쌈 ]
+------------------------+------------------------+------------------------+------------------------+
|     Word 3 (MSW)       |         Word 2         |         Word 1         |     Word 0 (LSW)       |
|  Pass & Render Queue   |   PSO & Shader Flags   |  Geometry & Submesh ID | Material & View Depth  |
|        (32-bit)        |        (32-bit)        |        (32-bit)        |        (32-bit)        |
+------------------------+------------------------+------------------------+------------------------+
```

* **기존 64비트 키의 한계:** 패스, 셰이더 변형(Variant) 플래그, 메시 ID, 서브메시 번호, 머티리얼 해시, 그리고 반투명 정렬 및 Early-Z 효율을 위한 카메라 깊이(Depth)까지 모두 담기에는 64비트 공간이 부족해 해시 충돌이나 정밀도 손실이 발생합니다.
* **128비트 키의 이점 (무료 상태 클러스터링):** 32비트 워드 4개로 확장하면 모든 상태 식별자를 손실 없이 담을 수 있습니다. 숫자의 크기순으로 정렬하는 것 만으로도 최상위 워드(Word 3 -> Word 2 -> Word 1)가 같은 패킷들이 물리적으로 인접하게 묶이므로 렌더 스레드는 **인접한 패킷 간 상태가 바뀌는 클러스터 경계에서만 바인딩 API를 호출**하면 됩니다.

문제는 **매 프레임 60,000개의 16바이트 키를 1ms 이내에 정렬해야 한다**는 점이었습니다.

---
{: style="clear: none;"}

## 2. 가설 검증: "정수 정렬이면 무조건 기수 정렬(Radix Sort)이 빠르지 않나?"

일반적으로 게임 엔진에서 정수형 정렬 키를 다룰 때는 비교 연산을 수행하지 않는 **LSD 기수 정렬(Radix Sort, O(k · N))**이 비교 기반 정렬인 `std::sort`(O(N log N))보다 빠르다고 알려져 있습니다.

하지만 직접 프로파일링을 수행한 결과 **키 크기가 4바이트(32-bit)에서 16바이트(128-bit)로 커지는 순간 기수 정렬이 `std::sort`보다 오히려 3배 이상 느려지는 역전 현상**을 확인했습니다. 원인은 빅오(Big-O) 표기법에 가려진 **패스 회전수**와 **메모리 계층 구조(Cache Locality)**에 있었습니다.

### 2.1. 패스 회전수(k=16)와 비교 정렬 탐색 깊이(log N ≈ 15.87)의 동조화
실무 엔진에서 기수 정렬은 L1 캐시 크기를 고려해 주로 **1바이트(8-bit = 256개 버킷)** 단위로 수행합니다.
* **4바이트(32-bit) 키: 4패스(k=4)** 만에 정렬이 끝납니다.
* **16바이트(128-bit) 키:** 1바이트씩 처리하므로 무조건 **16패스(k=16)**를 돌아야 합니다.
* 반면 컬링 후 정렬 대상 패킷 수 N = 60,000일 때 비교 기반 정렬(`std::sort`)의 분할 깊이인 log(60,000)은 **약 15.87**입니다. 즉, 키가 16바이트가 될 경우 **기수 정렬의 패스 회전수(16회)와 비교 정렬의 탐색 깊이(약 16단계)가 사실상 같아져** 기수 정렬의 회전수 이점이 사라집니다.

### 2.2. 결정적 병목: 16번의 전체 배열 핑퐁 복사와 Scatter 캐시 미스
회전수가 같아진 상태에서 우위를 바꾼 것은 **메모리 접근 패턴**이었습니다.

<pre class="mermaid" style="background: transparent; border: none; text-align: center;">
flowchart LR
    classDef bad fill:#7f1d1d,stroke:#ef4444,stroke-width:2px,color:#ffffff
    classDef good fill:#14532d,stroke:#22c55e,stroke-width:2px,color:#ffffff

    subgraph RADIX ["❌ 8-Bit LSD 기수 정렬 (16바이트 키 = 16 Passes)"]
        direction TB
        R1["매 패스마다 전체 배열(60,000개) 순회하며<br/>256개 버킷 히스토그램 및 Prefix Sum 계산"]:::bad
        R2["원본 버퍼 → 임시 버퍼(Ping-Pong)로<br/>256개 비연속 메모리 주소에 흩뿌려 쓰기 (Scatter Write)"]:::bad
        R3["결과: 총 32회 전체 배열 순회 + 약 30MB 메모리 복사<br/>캐시 라인(64B) 스래싱 및 대역폭 병목 (3.92ms)"]:::bad
        R1 --> R2 --> R3
    end

    subgraph SIMD_SORT ["✅ Branchless SIMD std::sort (제자리 정렬)"]
        direction TB
        S1["별도 임시 버퍼 없이 원본 배열 내에서<br/>파티션 구간을 좁혀가며 제자리(In-place) 교환"]:::good
        S2["파티션이 작아질수록 데이터가 L1/L2 캐시에 상주하며<br/>128-bit XMM 레지스터 내에서 단 몇 사이클 만에 비교"]:::good
        S3["결과: 추가 메모리 할당 0 Byte + 높은 캐시 적중률<br/>분기 예측 실패 제거로 고속 정렬 달성 (1.03ms)"]:::good
        S1 --> S2 --> S3
    end
</pre>

<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>
  if (typeof mermaid !== 'undefined') {
    mermaid.initialize({ startOnLoad: true, theme: 'dark' });
  }
</script>

1. **과도한 메모리 트래픽:** 기수 정렬은 1패스마다 ① 히스토그램 카운팅을 위한 전체 읽기(1회)와 ② 계산된 오프셋 위치로 실제 16바이트 데이터를 옮겨 적는 핑퐁 읽기·쓰기(2회)를 수행합니다. 이를 16패스 반복하면 60,000개 배열을 총 **32회 이상 순회**하며 수십 MB의 메모리 읽기·쓰기 트래픽을 발생시킵니다.
2. **256개 버킷 분산 쓰기(Scatter Write)로 인한 캐시 미스:** 데이터를 0~255번 바구니 위치로 흩뿌려 적을 때 서로 멀리 떨어진 최대 256개의 메모리 주소를 불규칙하게 오가며 쓰기 작업이 일어납니다. 이는 CPU의 64바이트 캐시 라인 적중률을 크게 떨어뜨리고 TLB 미스를 유발합니다.

---
{: style="clear: none;"}

## 3. 해결책: `std::sort` + 분기 없는(Branchless) SSE2 SIMD 비교자

기수 정렬의 메모리 복사 병목을 피하기 위해 캐시 지역성이 뛰어난 제자리 정렬(`std::sort`, Introsort)을 채택하되 일반 비교 함수가 가진 **CPU 분기 예측 실패(Branch Misprediction)** 문제를 해결했습니다.

### 3.1. 일반 `if-else` 비교 함수의 문제점
한 프레임에 그려지는 60,000개의 패킷 중 상당수는 **동일한 렌더 패스(`pass`)**와 **동일한 셰이더(`pipeline`)**를 공유합니다.

```cpp
// 일반 비교 방식: 상위 워드가 같을 때마다 조건 분기(if)가 연달아 발생
bool CompareNaive(const SortKey& a, const SortKey& b) noexcept {
    if (a.pass != b.pass)         return a.pass < b.pass;         // 분기 1
    if (a.pipeline != b.pipeline) return a.pipeline < b.pipeline; // 분기 2
    if (a.mesh != b.mesh)         return a.mesh < b.mesh;         // 분기 3
    return a.tieBreak < b.tieBreak;
}
```
위 코드는 두 패킷의 `pass`가 같으면 두 번째 `if`로, `pipeline`까지 같으면 세 번째 `if`로 넘어갑니다. 정렬 도중 어느 단계에서 승부가 날지 매 비교마다 불규칙하게 바뀌므로 CPU 파이프라인에서 **분기 예측 실패(1회당 약 15~20사이클 손실)**가 빈번하게 일어납니다.

### 3.2. SSE2 `movemask`를 활용한 128비트 무분기(Branchless) 비교 원리
16바이트(128비트) 정렬 키는 x86 CPU의 기본 **SSE2 레지스터(`__m128i`) 1개 크기와 일치**합니다. 이를 이용하면 4개의 32비트 워드를 동시에 비교한 뒤 이진수 자리수 가중치를 활용해 **한 번의 정수 비교**로 끝낼 수 있습니다.

1. **128비트 일괄 로드 및 부호 비트 보정:** `_mm_load_si128`로 16바이트 키 두 개를 레지스터에 올립니다. SSE2의 32비트 비교 명령어(`_mm_cmpgt_epi32`)는 부호 있는 정수(Signed) 기준이므로, 부호 없는 정수(`uint32_t`) 대소 관계를 유지하기 위해 최상위 부호 비트(`0x80000000`)를 `XOR`로 반전시킵니다.
2. **4개 워드 동시 비교 및 4비트 마스크 추출 (`_mm_movemask_ps`):** 4개 칸에 대해 `A < B`인 결과와 `A > B`인 결과를 동시에 구한 뒤 `_mm_movemask_ps`로 각 칸의 최상위 비트만 뽑아 **0~15 사이의 4비트 정수 두 개(`ltMask`, `gtMask`)**로 압축합니다.
   * **Bit 3 (가중치 2^3 = 8):** `pass` 비교 결과
   * **Bit 2 (가중치 2^2 = 4):** `pipeline` 비교 결과
   * **Bit 1 (가중치 2^1 = 2):** `mesh` 비교 결과
   * **Bit 0 (가중치 2^0 = 1):** `tieBreak` 비교 결과
3. **단일 정수 대소 비교로 종결:** 이진수의 수학적 성질상 상위 비트의 가중치는 하위 비트들의 합보다 항상 큽니다(예: 8 > 4 + 2 + 1). 따라서 앞자리 워드에서 이미 대소가 갈렸다면 뒷자리 워드 결과와 무관하게 마스크 정수의 대소가 결정되므로 `return ltMask > gtMask;` 한 줄로 4단계 우선순위 비교가 완벽히 끝납니다.

### 3.3. 실제 C++20 구현 코드 (`SortKey.h`)

```cpp
#pragma once
#include <immintrin.h>
#include <cstdint>
#include <algorithm>
#include <vector>

// 16바이트(128-bit) 정렬 보장 구조체
// Little-Endian(x86) 메모리 특성상 높은 주소(Lane 3)가 movemask의 최상위 비트(Bit 3, 가중치 8)에 매핑됨
struct alignas(16) SortKey {
    uint32_t tieBreak; // Lane 0 (Bit 0, 가중치 1): Material Hash (16b) | View Depth (16b)
    uint32_t mesh;     // Lane 1 (Bit 1, 가중치 2): Mesh ID (16b) | Submesh Index (16b)
    uint32_t pipeline; // Lane 2 (Bit 2, 가중치 4): PSO Handle & Shader Variant Flags
    uint32_t pass;     // Lane 3 (Bit 3, 가중치 8): Render Pass & Queue Priority (최상위 우선순위)
};

struct alignas(16) DrawPacket {
    SortKey  key;
    uint32_t entityIndex;
    uint32_t dynamicOffset;
    uint32_t set0Sentinel; // 0xF7F7F7F7 센티넬 마커 활용
    uint32_t set2Sentinel;
};

// 조건 분기(if/jmp)가 전무한 128-bit Branchless SIMD 비교자
__forceinline bool CompareSortKeySIMD(const SortKey& a, const SortKey& b) noexcept {
    // 1. 16바이트 키 전체를 단일 XMM 레지스터에 로드
    __m128i va = _mm_load_si128(reinterpret_cast<const __m128i*>(&a));
    __m128i vb = _mm_load_si128(reinterpret_cast<const __m128i*>(&b));

    // 2. uint32_t 대소 비교를 위해 부호 비트(0x80000000) 반전
    const __m128i signFlip = _mm_set1_epi32(static_cast<int>(0x80000000));
    __m128i vaSigned = _mm_xor_si128(va, signFlip);
    __m128i vbSigned = _mm_xor_si128(vb, signFlip);

    // 3. 4개 워드 병렬 비교 (a < b 및 a > b 동시 판별)
    __m128i ltVec = _mm_cmpgt_epi32(vbSigned, vaSigned);
    __m128i gtVec = _mm_cmpgt_epi32(vaSigned, vbSigned);

    // 4. 4비트 정수 마스크(0~15) 추출 후 단일 대소 비교로 우선순위 판정
    int ltMask = _mm_movemask_ps(_mm_castsi128_ps(ltVec));
    int gtMask = _mm_movemask_ps(_mm_castsi128_ps(gtVec));

    return ltMask > gtMask;
}
```

---
{: style="clear: none;"}

## 4. 실측 벤치마크 및 성능 비교 (Benchmark Results)

실제 프로젝트 구동 환경(AMD Ryzen 7 7700, 32GB DDR5, MSVC `/O2 /arch:AVX2` 릴리즈 빌드)에서 컬링 후 살아남은 **60,000개의 드로우 패킷**을 대상으로 1,000프레임 평균 정렬 소요 시간을 측정한 결과입니다.

| 정렬 방식 (N = 60,000, Key = 16 Bytes) | 평균 소요 시간 (ms) | 추가 버퍼 할당 | 핵심 병목 및 하드웨어 특성 | 상대 속도 |
| :--- | :---: | :---: | :--- | :---: |
| **1. 8-Bit LSD 기수 정렬 (16 Passes)** | `3.92 ms` | `~0.96 MB`<br>*(Ping-Pong)* | 16패스 전체 복사 및 256개 버킷 Scatter 쓰기로 인한 캐시 미스 | 1.0x (기준) |
| **2. `std::sort` + 일반 `if-else` 비교자** | `2.18 ms` | `0 Byte`<br>*(In-Place)* | 상위 워드(`pass`, `pipeline`) 동일 구간에서 CPU 분기 예측 실패 빈발 | 1.8x 빠름 |
| **3. `std::sort` + Branchless SIMD (채택)** | **`1.03 ms`** | **`0 Byte`**<br>*(In-Place)* | **L1/L2 캐시 상주 + 조건 분기 제거로 레지스터 내 초고속 비교** | **3.8x 빠름** |

### 파이프라인 상태 클러스터링 효과
정렬이 완료된 패킷 배열을 싱글 렌더 스레드가 순회하며 클러스터 경계에서만 상태를 바인딩하도록 처리한 결과, 프레임당 Vulkan 드라이버 호출 횟수가 다음과 같이 감소했습니다.
* **정렬 클러스터링 적용 전:** 서브메시 단위 개별 바인딩 시 최대 수만 회의 상태 전환 발생
* **128비트 정렬 + Set 0/1/2 분리 적용 후:** 프레임당 전체 디스크립터 셋 바인딩 호출 **200회 미만**, 정렬 소요 시간 **약 1.0ms 내외**로 30+ FPS 안정적 방어

---
{: style="clear: none;"}

## 5. 엔지니어링 회고 (Takeaways)

1. **알고리즘의 점근적 복잡도(O)보다 하드웨어 메모리 계층이 우선한다:**  
   이론상으로는 비교를 하지 않는 기수 정렬(O(k · N))이 유리해 보이지만, 키 크기가 16바이트로 커지면 k(16) ≈ log N(15.87)이 되어 회전수 이점이 사라집니다. 현대 CPU에서는 산술·비교 연산보다 **메모리 읽기·쓰기(Memory Wall)**가 수 ~ 수십 배 비싸기 때문에 16번의 전체 배열 복사를 수행하는 기수 정렬보다 L1/L2 캐시 안에서 제자리 정렬을 수행하는 `std::sort`가 훨씬 유리함을 데이터로 확인했습니다.
2. **데이터 규격을 하드웨어 레지스터 폭(128-bit)에 맞추면 분기가 사라진다:**  
   정렬 키를 정확히 128비트(16바이트)로 정렬(`alignas(16)`)해 설계한 덕분에 복잡한 다중 조건문을 단 몇 줄의 SSE2 SIMD 명령어와 비트 마스크 비교 하나로 치환할 수 있었습니다.

[⬅️ 메인 프로젝트(Endfield-Vulkan-Renderer) 아키텍처 포스트로 돌아가기](https://ji-hun-park.github.io/myendfield){: .btn .btn--primary}
