---
title: "WhatIsAVaccine"
permalink: /whatisavaccine/
layout: single
toc: true
toc_sticky: true
toc_label: "목차"
---

**"Generative AI와 게임 플레이의 결합 그리고 게임 속 그림판"**

## 0. Links
📥**Download:** [WhatIsAVaccine GoogleDrive](https://drive.google.com/file/d/1UxYPVTVv3ubTJkSkJFCheksu-1MviBIz/view?usp=sharing)  
🎬**Play Video:** [WhatIsAVaccine Play Video](https://youtu.be/HhLgUsnNSzc)  
💻**Github:** [WhatIsAVaccine Github](https://github.com/ji-hun-park/Weru_GameJam_R1)  
~~itch.io: [WhatIsAVaccine itch](https://jihunpark.itch.io/whatisavaccin)~~

## 1. 프로젝트 개요
> LLM(대형 언어 모델) API를 활용하여, 플레이어의 행동(질문, 그림 그리기)이 게임 진행에 직접적인 영향을 미치는 **3D 추리 서바이벌 게임**입니다.  
> 기존의 정해진 스크립트 방식에서 벗어나, AI가 상황을 분석하고 동적으로 힌트를 제공하는 시스템을 구현하는 데 중점을 두었습니다.

게임잼에 참여해 촉박한 기간 안에 기존에 개인적으로 연습했던  
프로젝트의 기능 들을 통합해보는 경험을 해봤습니다.

**시스템 흐름도**  
![시스템 흐름도](https://ji-hun-park.github.io/assets/images/Game_Loop_with_API.png "Game Logic Flow"){: .align-center}

### 📋프로젝트 정보

* **장르 / 플랫폼:** 3D 추리 서바이벌 / PC (Single)
* **개발 인원:** 1인 개발
* **개발 기간:** 2025.1~2025.2 약 1개월 소요(연습용 게임, 코드 개선 기간 포함)
* **사용 엔진 / 언어 / 환경:** Unity6 (6000.0.32f1) / C# / VSCode

## 2. 게임 소개
**Unity 3D 추리 그림 그리기 서바이벌 게임**  
![그림41](https://ji-hun-park.github.io/assets/images/pofol041.png "그림41"){: .align-center}

**[시놉시스]**  
미지의 공간으로 전송돼 고립된 주인공은 적에 의해 감염된다.  
독이 퍼지기 전에 해당 공간의 AI를 통해 키워드를 알아내  
스캐너에게 그림을 전송해 해석시켜 해독제를 얻어야 한다.

### 사용 에셋
무료 3D 에셋 사용 및 일부를 변형하거나 수제작했습니다.

## 3. 플레이 방식
**초기 애니메이션**
![그림03](https://ji-hun-park.github.io/assets/images/pofol003.png "그림03"){: .align-center}

**힌트 목록**
![그림04](https://ji-hun-park.github.io/assets/images/pofol004.png "그림04"){: .align-center}

**그림 그리기**
![그림05](https://ji-hun-park.github.io/assets/images/pofol005.png "그림05"){: .align-center}

**NPC의 그림 해석**
![그림06](https://ji-hun-park.github.io/assets/images/pofol006.png "그림06"){: .align-center}

* **조작**
    * WASD로 8방향 이동 가능
    * Q,E를 통해 이동 방향의 상하, 좌우 반전 가능
    * U,I,O,J,K,L 키를 통해 카메라 시점 변경 가능
    * 마나를 이용해 점프(스페이스 바)와 대쉬(쉬프트) 가능(마나는 자동으로 서서히 회복)
* **전투**
    * 플레이어는 공격 불가
    * 적의 투사체에 맞을 경우 HP감소
    * HP가 0이 되거나 제한 시간을 초과하면 게임 오버
* **추리**
    * 맵 구석에 AI를 탑재한 NPC가 존재
    * NPC와 상호 작용을 통해 각각 1번씩만 질문이 가능(총 5개 존재)
    * 답변을 통한 힌트로 키워드 유추 가능
    * 추후 메뉴 창을 통해 답변을 다시 볼 수 있음
* **검증**
    * 맵 중앙 스크린에 상호 작용해 캔버스 활성화
    * 캔버스에 키워드를 연상시키는 그림을 그려 먼저 저장한 다음 전송
    * 스캐너에게 상호 작용 후 그림의 대한 해석을 들음
    * 해석에서 키워드와 일치하는 부분은 빨간색으로 표시
    * 키워드가 포함되면 클리어, 없으면 게임 오버

## 4. 핵심 구현 내용
- LLM API에게 질문과 키워드를 전송해 힌트를 답변으로 받는 기능
    - FortheGranada 프로젝트에서 상세 설명
- 모델의 응답에서 답변을 파싱해 내용중에서 키워드와 일치하는게 있는지 string 매칭을 해서 일치하는 부분을 빨간색 글씨로 교체하는 기능
- 그림판 기능(하단 파트에서 설명)

### 핵심 기능(캔버스)
- RawImage를 이용해 Texture2D의 각 픽셀의 색을 업데이트하는 방식으로 그리기 구현
- 마우스 커서 위치 좌표 동기화
    - Raycast를 통해 3D 오브젝트(캔버스)의 UV 좌표를 검출하고, Texture2D.SetPixel을 사용하여 텍스처를 직접 수정하는 방식으로 드로잉 시스템 구현
- 다양한 색 제공 및 지우기 기능 구현
- 시작 좌표와 끝 좌표를 통해 도형 그리기 기능 구현
- 좌표를 통해 영역 선택 및 영역 제거 기능 구현
- 파일 시스템으로 이미지 저장 기능 구현
- 저장 기능을 통해 UNDO REDO 구현
- LLM API를 통해 이미지 전송 및 해석 기능 구현

## 5. 기본 구현 내용
- 4방향 캐릭터 이동 구현 및 대각선 이동 속도 정규화
- 점프와 대쉬 기능과 그를 위한 마나 구현
- 카메라 이동 및 제어 기능
- 시간 제한 기능
- 커스텀 쉐이더 제작
- 캐릭터 접근 시 메테리얼 변경 기능
    - OnTriggerEnter로 근접 여부 판별 후 미리 셋된 메테리얼 스위칭
- 몬스터가 플레이어를 인식해 이동, 투사체 발사 등 전투 기능
- 힌트들을 볼 수 있는 스크롤 UI 구현

## 6. 트러블 슈팅
### 물리 충돌과 프레임 동기화 문제 (Tunneling Effect)
- 문제: 캐릭터가 벽을 통과하는 버그 발생
- 원인: `Transform.Translate` 또는 일반 `Update` 문에서의 이동 처리가 물리 연산 주기와 어긋나거나, Collider가 얇아 프레임 사이를 건너뛰는 현상으로 파악
- 해결: 프로젝트 설정에서 물리 연산의 `Default Contact Offset` 값을 조정하여 안정성 확보  

### 렌더링 최적화 및 카메라 설정
- 문제: 원경 오브젝트가 짤리거나, 불필요한 연산이 발생하는 문제
- 원인: 맵의 크기가 지나치게 큼
- 해결: camera의 Far Clip Plane을 적절히 조절하고, 벽 오브젝트 간격 조절
- 개선점: 시야각(FOV) 밖의 객체 렌더링을 제외하는 Frustum Culling이 효과적으로 동작하도록 맵 구조를 개선하거나 Occlusion Culling 도입 고려

## 7. 코드 개선 및 리팩토링

### 📢 이벤트 기반 (Event-Driven) 설계로의 전환

**플래그 사용**  

![EventDriven](https://ji-hun-park.github.io/assets/images/pofol033.png "EventDriven"){: .align-center}

* **기존:** `Update` 문에서 매 프레임 상태를 체크하는 폴링(Polling) 방식 사용. (불필요한 연산 낭비)
* **개선:** C# `Action` 및 Unity `UnityEvent`를 활용한 **옵저버 패턴(Observer Pattern)** 적용.
    * 객체 간의 결합도(Coupling)를 낮추고 유지보수성을 향상시켰습니다.

### 📦 어드레서블(Addressables) 에셋 시스템 도입 시도

**어드레서블 사용**

![Addressables](https://ji-hun-park.github.io/assets/images/pofol034.png "Addressables"){: .align-center}

* **목적:** 게임 리소스의 효율적인 메모리 관리와 로딩 최적화.
* **적용:** 기존 `Resources.Load` 방식의 메모리 비효율성을 개선하기 위해, 주요 텍스처 및 프리팹을 Addressable Asset으로 변환하여 비동기 로딩 및 참조 카운팅을 통한 메모리 해제 자동화를 시도했습니다.
