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

## Introduction
유닉스와 리눅스의 발전 과정에 대해 알아보겠습니다.  
왜 역사를 배워야하는가? 의문이 드실 수 있습니다.  
하지만 지금까지 발전해 온 과정을 안다면 리눅스가 왜 탄생했고, 어떤 과정을 통해 지금에 이르렀는지 알게되어 리눅스에 더욱 흥미를 느낄 수 있을 것 입니다.  
또한 현재 기술이 어떤 고민과 시행 착오를 통해 개선되어 왔는지 알 수 있으니  
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
그러나 후에 실패하게 됩니다.

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
`Thompson`과 `Ritchie`가 `ACM 커뮤니케이션`에 `UNIX`를 출판했습니다.  
* 이로 인해 추후 `Turing Award, 튜링상`을 수상했습니다.

#### 1977
UNIX 개발에서 분열  
`UC-Berkeley(컴퓨터 과학으로 유명하고 스탠포드와 쌍벽을 이룸)`가 유닉스 소스 코드를 받아 `Bill Joy`가 한 단계 업그레이드한 자체 버전인 `Berkeley Software Distribution(BSD)`을 출시했습니다.  
* `vi`, `shell`, `curses`와 같은 많은 유틸리티를 개발했습니다.  
* 추후 `썬 마이크로시스템즈`를 공동 창립했습니다.

#### 1980
`Microsoft`가 `Xenix`라는 자체 버전의 `Unix`로 OS 사업에 진출했습니다.

#### 1981
`IBM`이 `Microsoft`에 새로운 PC 라인용 OS를 개발하는 계약을 제공했습니다.

### 1982
AT&T는 이제 상업적 사용을 위해 버전 7을 기반으로 한 UNIX System III를 라이선스했으며, 첫 번째 버전이 출시되었습니다.  
`Bill joy(BSD)`는 `Sun Microsystems`를 공동 창립하고 `Andreas Bechtolsheim`과 `Vinod Khosla`와 함께 `SunOS(현재 Solaris)`를 만들었습니다.  
* 그들은 Unix 기반 워크스테이션을 개발했습니다.

### 1983
`4.2BSD`버전: `TCP/IP` 및 `소켓` API의 첫 번째 릴리스.(미 국방성에 납품)  
미국 법무부는 `AT&T`에 대한 두 번째 `반독점(큰 회사를 쪼개는 것)` 소송을 해결하고 `Bell System`을 해체했습니다.(AT&T와 Bell로 나뉘어졌습니다.)  
`AT&T`는 즉시 `Unix System V`를 상용화(상업화)하기 위해 서둘렀습니다.  
`Thompson`과 `Ritchie`는 일반 `운영 체제 이론`을 개발하고 특히 `UNIX 운영 체제`를 구현한 공로로 `Turing Award, 튜링상`을 공동 수상했습니다.

### 1984-1987
![그림08](https://ji-hun-park.github.io/assets/images/그림09.jpg "그림08"){: .align-center}

#### 1984
유닉스 버전이 너무 많아져서 호환이 안되는 문제가 생겼습니다.  
그래서 `X/Open 컨소시엄`을 설립해 표준화를 시도했습니다.  
그들의 목표는 UNIX에 기반한 개방형 시스템 사양을 만드는 것이었습니다.

#### 1985
네덜란드 `Andrew Tanenbaum` 교수는 `Intel` PC용 `i386` 기반 `MINIX`라는 `Unix`와 유사한 `운영 체제(OS)` 텍스트북(교재)`(초소형 OS(미니 유닉스))`을 작성했습니다.  
* `MINIX`는 또한 `Linux` `커널`의 생성에 영감을 주었습니다.

`Linus Benedict Torvalds(리누스 토르발스, 핀란드 출생, 스웨덴계)`가 `MINIX`를 열심히 공부해 `리눅스 커널`을 개발했습니다.  
* `유닉스`랑 `커널`은 같으나 `라이선스`가 전혀 다름

#### 1987
`AT&T`와 `Sun Microsystems`는 `X/Open`과 독립적으로 `Xenix, BSD, SunOS` 및 `System V`의 기능을 `System V Release 4(SVR4)`로 병합하기 시작했습니다.

### 1988
`Open Software Foundation`은 `Mach`와 `BSD`를 기반으로 한 `표준 Unix 구현체`인 `OSF/1`을 출시했습니다.  
같은 해에 `AT&T`와 다른 라이선스 그룹은 `UNIX International`을 결성하여 이에 대응했습니다.  
`X/Open`은 중립적인 태도를 취했습니다.  
`Unix International`협회에선 이를 하나로 통합하자는 운동이 일어났습니다.  
`IEEE(국제 전기 전자 학회)`의 `POSIX` 사양`(IEEE Std 1003.1-1988)`이 공개되었고 `표준`으로 합쳐졌습니다.

### 1991
![그림09](https://ji-hun-park.github.io/assets/images/그림10.jpg "그림09"){: .align-center}
`Richard Matthew Stallman(리처드 스톨먼, 자유 소프트웨어 파운데이션 주장, 카피 레프트)`이 시작한 `FSF(자유 소프트웨어 재단)`는 자연스럽게 리눅스 커널을 채택하여 `GNU` 시스템을 완성했습니다.  
`리누스 토르발스`는 `리눅스`를 `오픈소스` 제품으로 인터넷에 출시했습니다.  
>GNU = GNU is Not UNIX(유닉스랑 다르지만 똑같다, 재귀적 명칭)

![그림10](https://ji-hun-park.github.io/assets/images/그림11.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림12.jpg "그림11"){: .align-center}

### 1993
`UI(UNIX International)`와 `OSF(Open Software Foundation)`의 주요 참여자들이 `COSE(Common Open Software Environment)` 연합을 결성했습니다.  
6월에 `AT&T`는 `UNIX` 자산을 `Novell(네트워크사)`에 매각했습니다.  
거기에 그치지않고, `Novell`은 구매한 `UNIX®` 상표 및 인증 권한을 `X/Open Consortium`에 양도하기로 결정했습니다.  
`Novell`은 이를 이용해 `Windows NT`와 싸우려고 했지만 핵심 시장은 상당한 타격을 입었습니다.  
`마이크로 소프트`사는 `Windows NT`에 88년 `카네기 멜런` 대학교에서 만든 `Mach`라는 가볍고 효율적이고 퍼포먼스 좋은 커널을 채용했습니다.

### 1994
`UI`와 `OSF`가 합병되었고, 합병된 단체는 `OSF`라는 이름을 유지했습니다.  
`X/Open`은 `System V`를 기반으로 `Spec1170`을 발표했습니다.

### 1995
기존 `UNIX 라이선스`를 관리하고 지원하는 사업과 `System V` 코드 기반을 추가로 개발할 수 있는 권한은 `Novell`이 `Santa Cruz Operation(SCO)`에 매각했습니다.

### 1996
`X/Open`과 새로운 `OSF`가 합병하여 `Open Group`을 형성했습니다.

### 1997
`Open Group`이 `Single UNIX Specification Version 2`를 출시했습니다.(최종)

### 1998
`Open Group`과 `IEEE`가 `Austin Group`을 시작했습니다.
`Austin Group`이 `POSIX`와 `Single UNIX Specification`의 `통합 표준`을 개발하기 시작했습니다.

### 2000
`SCO(Santa Cruz Operation)`가 전체 `UNIX` 사업과 자산을 `Caldera Systems`에 매각했습니다.

### 2001
![그림12](https://ji-hun-park.github.io/assets/images/그림13.jpg "그림12"){: .align-center}
[UNIX](https://www.unix.org/)
>Compliance
>>AIX, HP/UX, Mac OS X, SCO, Solaris, Tru64 UNIX, z/OS …
>>>AIX(IBM UNIX), HP/UX(Hewlett Packard Enterprise), Mac OS X(Mach), Solaris(사라짐)

`오스틴 그룹`에서 `Single UNIX Specification Version3` 출시(현재는 4)  
* The Open Group Base Specifications Issue 6
* IEEE Std 1003.1-2001(POSIX.1, 유닉스 표준안)
* ISO/IEC 9945:2002(동일한 문서입니다!)

### 2008
![그림13](https://ji-hun-park.github.io/assets/images/그림14.jpg "그림13"){: .align-center}
2008년 12월, `오스틴 그룹`은 `POSIX:2008(공식적으로 IEEE Std 1003.1-2008)` 기반 새로운 주요 개정판인 `Single UNIX Specification, Version 4(SUSv4)`를 발표했습니다.  

## Evolution of Unix and Unix-like systems
![그림14](https://ji-hun-park.github.io/assets/images/그림15.jpg "그림14"){: .align-center}
* UNIX 계열의 발전 과정도입니다!

## Major Types of Unix
![그림15](https://ji-hun-park.github.io/assets/images/그림16.jpg "그림15"){: .align-center}
>BSD4.3(오픈소스)로 스티브 잡스가 넥스트스텝, 오픈스텝을 만들고 그걸 애플에 팔면서 Mac OS가 만들어졌습니다.  
>>그러나 리눅스는 독자적(공짜)입니다.

## Assignment
### Install Freeware UNIX
* Solaris
  * ~~https://www.sunfreeware.com~~
* BSD
  * <https://www.freebsd.org/>
  * <https://www.openbsd.org/>
  * <https://www.netbsd.org>
* Linux
  * <https://distrowatch.com/>  : Linux-ranking site
  * <https://cygwin.com>
  * <https://www.ubuntu.com/>
  * <https://fedoraproject.org/>
  * <https://www.opensuse.org/>

### Virtual System
Commercial Software
<https://vmware.com/>

Open Source Software 
<https://www.virtualbox.org/>

## Appendix
### Standard Issue

### UNIX Standard
![그림16](https://ji-hun-park.github.io/assets/images/그림17.jpg "그림16"){: .align-center}
### Symbols For Standard Programming
![그림17](https://ji-hun-park.github.io/assets/images/그림18.jpg "그림17"){: .align-center}
#### \_SUVREQ_H\_
```c
#ifndef _SUVREQ_H_
#define _SUVREQ_H_

#ifdef _POSIX_SOURCE /* tmp */
#error
#endif

#if defined(SUV_POSIX1990)
#define _POSIX_SOURCE
#define _POSIX_C_SOURCE 1

#elif defined(SUV_POSIX1993)
#define _POSIX_SOURCE
#define _POSIX_C_SOURCE 199309L

#elif defined(SUV_POSIX1996)
#define _POSIX_SOURCE
#define _POSIX_C_SOURCE 199506L

#elif defined(SUV_SUS1)
#define _POSIX_SOURCE
#define _POSIX_C_SOURCE 2
#define _XOPEN_SOURCE
#define _XOPEN_SOURCE_EXTENDED 1

#elif defined(SUV_SUS2)
#define _POSIX_SOURCE
#define _POSIX_C_SOURCE 199506L
#define _XOPEN_SOURCE 500
#define _XOPEN_SOURCE_EXTENDED 1

#elif defined(SUV_SUS3)
#define _POSIX_SOURCE
#define _POSIX_C_SOURCE 200112L
#define _XOPEN_SOURCE 600
#define _XOPEN_SOURCE_EXTENDED 1
#endif

#endif /* _SUVREQ_H_ */
```
### What standard does your system support?
표준에 따라 애플리케이션을 작성하기 전에 OS에서 제공하는 내용을 확인해야 합니다.  
UNIX는 이를 위해 `unistd.h`에 포함된 일부 심볼을 ​​제공합니다.
- _POSIX_VERSION :
- _XOPEN_UNIX :
- _XOPEN_VERSION :3(XPG3), 4(SUSV1)

#### SUV_SUS3
```c
#define SUV_SUS3

#if 1
#include "suvreq.h"
#endif
#include <unistd.h>
#include <stdio.h>

int main(void){
  printf("Request:\n");

  #ifdef _POSIX_SOURCE
    printf("\t_POSIX_SOURCE defined\n");
    printf("\t_POSIX_C_SOURCE = %ld\n", (long)_POSIX_C_SOURCE);
  #else
    printf("\t_POSIX_SOURCE undefined\n");
  #endif

  #ifdef _XOPEN_SOURCE
    #if _XOPEN_SOURCE == 0
      printf("\t_XOPEN_SOURCE defined (0 or no value)\n");
    #else
      printf("\t_XOPEN_SOURCE = %d\n", _XOPEN_SOURCE);
    #endif
  #else
    printf("\t_XOPEN_SOURCE undefined\n");
  #endif

  #ifdef _XOPEN_SOURCE_EXTENDED
    printf("\t_XOPEN_SOURCE_EXTENDED defined\n");
  #else
    printf("\t_XOPEN_SOURCE_EXTENDED undefined\n");
  #endif

  printf("Claims:\n");
  #ifdef _POSIX_VERSION
    printf("\t_POSIX_VERSION = %ld\n", (long)_POSIX_VERSION);
  #else
    printf("\tNot POSIX\n");
  #endif

  #ifdef _XOPEN_UNIX
    printf("\tX/Open\n");
    #ifdef _XOPEN_VERSION
      printf("\t_XOPEN_VERSION = %d\n", _XOPEN_VERSION);
    #else
      printf("\tError: _XOPEN_UNIX defined, but not _XOPEN_VERSION\n");
    #endif
  #else
    printf("\tNot X/Open\n");
  #endif

  return 0;
}
```

## 마무리
이상으로 Unix와 Linux의 역사편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
