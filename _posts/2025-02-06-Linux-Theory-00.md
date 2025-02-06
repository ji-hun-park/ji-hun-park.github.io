---
title:  "Linux 기초 이론 00 History"
excerpt: "Linux 기초 이론 영번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux, Lecture, Theory
last_modified_at: 2025-02-06T18:05:00-00:00
---

# Linux Lecture Theory 00 History
## Intro
유닉스와 리눅스의 발전 과정에 대해 알아보겠습니다.  
이러한 역사를 안다면 현재 기술이 어떤 고민과 시행 착오를 통해 개선되어 왔는지 알 수 있으니  
이해도가 높아지고, 추후 이를 응용할 때 방향성을 잡기도 용이할 것 입니다.

## Before UNIX
### 1955
리눅스는 1970년대에 처음 등장했으며, 1955년에 `존 맥카시`가 타임 셰어링 개념의 기원이 되는 아이디어를 최초로 고안했습니다.  
그의 아이디어는 CTSS 개발에 영향을 미쳤습니다.
![그림01](https://ji-hun-park.github.io/assets/images/그림03.jpg "그림01"){: .align-center}
* 존 매카시
  * 1927년 9월 4일 매사추세츠주 보스턴 출생
  * 미국의 컴퓨터 과학자이자 인지 과학자
  * 1971년 주요 AI 기여로 튜링상을 수상
  * Lisp 프로그래밍 언어의 발명가

+ 미국 정부는 AT&T에 대한 무역 제한을 부과하는 학위를 통과시켰습니다.
  + 본 회사는 비통신 사업에서 수익을 창출하는 것이 허용되지 않았습니다.

### 1962
MIT의 계산 센터는 `CTSS(호환 타임셰어링 시스템)`라는 최초의 `타임셰어링 운영 체제`를 개발했습니다.  
CTSS는 후속작 `MULTICS(MULTiplexed Information and Computing Service)` 개발에 영감을 주었습니다.  
~~(그러나 후에 실패하게 됩니다.)~~

## History
### 1969 - 1971
![그림02](https://ji-hun-park.github.io/assets/images/그림02.jpg "그림02"){: .align-center}
`Ken Thompson`, `Dennis MacAlistair Ritchie(C와 유닉스의 아버지)`를 포함한 `GE, MIT, Bell Labs`의 연구원들이 `MULTICS`라는 야심찬 `메인프레임`용 `OS`를 출시했습니다.  
이는 `GE-645`에서 실행되도록 설계되었습니다.  
그러나 실패했습니다.

`Ken Thompson`은 `GE-645 메인프레임`을 위해 개발을 계속했고,  
`AT&T 연구소`에서 `Space Travel`이라는 컴퓨터용 게임을 작성했습니다.  
하지만 그는 게임이 `GE 기계`에서는 너무 느리고 비쌌다는 것을 알게 되었습니다.  
그는 `Bell Labs`에서 요구 사항에 맞는 거의 사용되지 않는 `PDP-7`을 찾았습니다.  
이 `PDP7`라는 성능이 매우 안좋은 컴퓨터에서 `Thompson`이 `Bell Labs`를 만들어  
`Rudd Canaday`와 `Dennis Ritchie`와 함께 유닉스 시스템 개념을 고안하고 유닉스라는 데이터를 만들었습니다.
* 계층적 파일 시스템, 컴퓨터 프로세스 및 장치 파일 개념, 명령줄 인터프리터 및 일부 소규모 유틸리티 프로그램을 개발했습니다.
* UNICS -> UNIX 명칭 변경

`Brian Kernighan(데니스 리치와 함께 C 프로그래밍 언어 첫 번째 책 공동 집필자)`은 `MULTICS`에서 따온 `UNICS(Uniplexed Information and Computing Service)`라는 프로젝트 이름에 참여했습니다.

1970년에 처음으로 `Unix 운영 체제`가 공식적으로 명명되어 `PDP-11/20`에서 실행되었습니다.  
* UNIX의 첫 번째 버전은 `어셈블리 언어`로 작성되었습니다.

`AT&T`, `Bell Labs`는 라이선스에 따라 라이선스를 허용하는 모든 대학과 상업 회사, 미국 정부에 UNIX를 제공했습니다.  
라이선스에는 `PDP-11` 어셈블리 코드로 작성된 커널의 머신 종속 부분을 포함한 모든 소스 코드가 포함되었습니다.  
당시엔 귀중했던 OS를 여기저기에 뿌려버리고 소스 코드까지 주니, 여기저기서 연구하기 시작했습니다.

![그림03](https://ji-hun-park.github.io/assets/images/그림04.jpg "그림03"){: .align-center}
* GE-645

![그림04](https://ji-hun-park.github.io/assets/images/그림05.jpg "그림04"){: .align-center}
* PDP-7

### 1973
`켄 톰슨`은 `B`라고 불리는 매우 간단한 언어를 작성했습니다.  
그는 이를 `PDP-7`에서 시도했고, 작동했지만 문제가 있었습니다.  
데니스 리치는 `B`를 발전시켜 최초의 `C 컴파일러`를 작성했습니다.  
켄 톰슨은 그와 팀을 이루어 UNIX 커널을 `어셈블리`에서 `C`로 다시 작성했습니다.  
* 로우에서 하이 레벨 랭귀지로 변함
* 하드 웨어 독립 부분만 어셈블러고 나머지 아닌 부분은 C로 작성, CPU가 달라도 다 돌아감

다른 머신에서 작동하도록 Unix를 수정하는 것이 더 쉬웠고(따라서 이식 가능해짐), 다른 개발자는 자유롭게 변형이 가능했습니다.

![그림05](https://ji-hun-park.github.io/assets/images/그림06.jpg "그림05"){: .align-center}
* Dennis Ritchie (standing) and Ken Thompson begin porting UNIX to the PDP-11 via two Teletype 33 terminals.

### 1974 - 1981
![그림06](https://ji-hun-park.github.io/assets/images/그림07.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/그림08.jpg "그림07"){: .align-center}

#### 1974
`Thompson`과 `Ritchie`가 `ACM 커뮤니케이션`에 `UNIX`를 출판했고,  
이로 인해 1977년 튜링상을 수상했습니다.
#### 1977
UNIX 개발에서 분열  
`UC-Berkeley(컴퓨터 과학으로 유명하고 스탠포드와 쌍벽을 이룸)`가 유닉스 소스 코드를 받아 `Bill Joy`가 한 단계 업그레이드한 자체 버전인 `Berkeley Software Distribution(BSD)`을 출시했습니다.  
* `vi`, `shell`, `curses`와 같은 많은 유틸리티를 개발했습니다.  
* 추후 `썬 마이크로시스템즈`를 공동 창립했습니다.

#### 1980
`Microsoft`가 `Xenix`라는 자체 버전의 `Unix`로 OS 사업에 진출했습니다.
#### 1981
`IBM`이 `Microsoft`에 새로운 PC 라인용 OS를 개발하는 계약을 제공했습니다.

![그림08](https://ji-hun-park.github.io/assets/images/그림09.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/그림10.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/그림11.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림12.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/그림13.jpg "그림12"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/그림14.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/그림15.jpg "그림14"){: .align-center}
