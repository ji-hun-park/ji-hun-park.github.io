---
title:  "Linux 기초 실습 04 \Ma\ke\fi\le"
excerpt: "Linux 기초 실습 04 \Ma\ke\fi\le 만들기"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Practice
last_modified_at: 2025-02-13T08:00:00-00:00
---

# Linux Lecture Practice 04 Makefile

## Introduction
이번 실습에서는 GCC를 이용한 컴파일 방법과 상용 CC를 이용한 컴파일 방법,  
컴파일 자동화 도구인 Make에 필요한 Makefile에 대해 알아보겠습니다.

## Compile
1. 소스 파일 생성
2. 전처리기 (CPP, C Pre Processor)  
Compile 전에 작동하여 #include, #define, 주석 제거 등을 담당
3. 컴파일러 (CC, C Compiler)  
전처리기를 거친 소스 파일을 assembly어 파일로 변환
4. 어셈블러 (AS, ASsembler)  
Assembly어 파일을 object 파일로 변환
5. 링커 (LD, Linker Directive) & 재배치 (Relocation)  
Object 파일들을 묶어 하나의 실행 파일을 생성  
Object 파일을 실제 주기억 장소에 맞추어 재배치 (상대주소를 절대주소로 변경)  
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG005.jpg "그림01"){: .align-center}

## 컴파일 하기
- cc hello.c
  - 실행 파일은 **a.out**으로 출력 (권한은 755)
- cc hello.c –o hello.out
  - 실행 파일은 **hello.out**으로 출력 (권한은 755)
- Linux 계열에선 cc 대신 gcc를 이용해도 됨
  - 사용법은 동일
- cc hello.c –O(옵션)
  - -O0 ~ -O3 : 최적화 수준 설정
  - -Ofast : 실행 속도 위주로 최적화
- cc hello.c –c
  - 컴파일만 진행 (실행 파일을 만들지 않음)
  - 결과물(목적 코드, 모듈)은 **hello.o** 파일로 출력
- cc hello.c –S
  - 어셈블리 코드로 생성
  - 결과물은 **hello.s** 파일로 출력

### Option
- -c			&nbsp;: compile only (linking 안함)
- -g 		&nbsp;: debug
- -help		&nbsp;: help
- -Ipathname	&nbsp;: 헤더파일의 경로 지정
- -llibrary		&nbsp;: lib 추가
- -Ldirectory	&nbsp;: lib 경로 지정
- -w		&nbsp;: 경고 무시
- -static		&nbsp;: static linking(default : dynamic)

```
ex) gcc –I/usr/local/cuda/include  
        –L/usr/local/cuda/lib64
        –o main main.c
        –lOpenCL
```

### 자세한 매뉴얼
<https://gcc.gnu.org/onlinedocs/>

### Files
- a.out		&nbsp;: default output file by compile
- file.a		&nbsp;: archive file
- file.c		&nbsp;: C source file
- file.c \|.cc\|.cpp	&nbsp;: C++ source file
- file.i		&nbsp;: 전처리 후 C source file
- file.o		&nbsp;: object file
- file.s		&nbsp;: assembler source file

## Example
```
$ gcc --help
$ gcc hello.c
$ gcc –o hello hello.c
$ gcc –c hello1.c hello2.c
$ gcc –o hello hello.c hello1.o hello2.o libtest.a
$ gcc –g hello.c
$ gcc –I../home/in –L../home/lib hello.c –ltest
$ gcc –static hello.c
```

## gcc를 이용한 간단한 컴파일
1. hello.c 로 파일을 하나 작성합니다.  
```c
// hello.c
#include <stdio.h>
int main()
{
    printf("Hello, World!\n");
    return 0;
}
```
2. gcc hello.c –o hello.out 을 통해 실행 파일을 생성하고,  
       ./hello.out을 입력하여 실행화면을 확인합니다.  

- 참고 : gcc hello.c 로 실행하면 이름을 자동으로 부여하기에 **a.out** 파일로 생성됩니다. 

![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG006.jpg "그림02"){: .align-center}

## 예제 – diary_exe 파일 만들기
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG007.jpg "그림03"){: .align-center}

### diary.h 헤더파일
```
 vi diary.h, memo.c, calendar.c, main.c
```
```c
// diary.h
#include <stdio.h>

void memo();
void calendar();
```
```c
// memo.c
#include "diary.h"

void memo()
{
    printf("I'm function Memo! \n");
}
```
```c
// calendar.c
#include "diary.h"

void calendar()
{
    printf("I'm function Calendar! \n");
}
```
```c
// main.c
#include "diary.h"

int main()
{
    memo();
    calendar();
    return 0;
}
```

### 생성된 파일 확인하기
ls 명령어를 사용해 파일이 제대로 생성되었는지 확인  
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG008.jpg "그림04"){: .align-center}

## 예제 – make 없이 컴파일해보기
- C 파일에서 Object 파일 생성하기
  - gcc –c –o memo.o memo.c
  - gcc –c –o calendar.o calendar.c
  - gcc –c –o main.o main.c

![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG009.jpg "그림05"){: .align-center}
- 생성된 Object 파일을 묶어 컴파일을 통해 diary_exe 실행파일 생성하기
  - gcc –o diary_exe main.o memo.o calendar.o
  - ls 명령어를 통해 확인

![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG010.jpg "그림06"){: .align-center}
- diary_exe 파일 실행시켜보기
  - ./diary_exe

![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG011.jpg "그림07"){: .align-center}

## 현재경로를 PATH에 추가하기
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG012.jpg "그림08"){: .align-center}
**현재 경로가 환경변수 PATH에 존재하지 않기 때문!**  
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG013.jpg "그림09"){: .align-center}
- bashrc의 내용을 적용시키기 위해선,
1. shell을 재실행
2. bashrc의 내용을 적용시키는 source 명령
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG015.jpg "그림11"){: .align-center}


## Make?
- Unix 계열에서 많이 사용하는 빌드 자동화 도구
- 매니페스트 파일을 이용하여 과정을 자동화
  - Manifest, 특정한 목적 및 절차를 표현하는 명세서
- 비슷한 도구
  - CMake
  - Java : Ant
  - IDE : Visual Studio, Eclipse, …

## 예제 – make를 이용한 컴파일
- Makefile의 구조
  - 목적파일(Target) : 명령어가 수행되어 나온 결과를 저장할 파일
  - 의존성(Dependency) : 목적파일을 만들기 위해 필요한 재료
  - 명령어(Command) : 실행되어야 할 명령어들
  - 매크로 (Macro) : 코드를 단순화시키기 위한 방법

![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG016.jpg "그림12"){: .align-center}
- Makefile의 작성규칙

![그림13](https://ji-hun-park.github.io/assets/images/LNXIMG017.jpg "그림13"){: .align-center}
- 매니페스트 형식

![그림14](https://ji-hun-park.github.io/assets/images/LNXIMG018.jpg "그림14"){: .align-center}

### Makefile lines
- dependency line	
	- target : [prerequisites]
- command line
	- “tab”  [@]   command
- macro definition
	- name = string
- include statement
	- include filenames
- comment
  - \# 이건 코멘트임

### Makefile 작성
>**vi Makefile**

![그림15](https://ji-hun-park.github.io/assets/images/LNXIMG019.jpg "그림15"){: .align-center}

### Make 사용법
#### make (목표 이름) –f (매니페스트 파일)
- 목표 이름 : 자동화할 목표를 지정
  - 생략 시 최초에 서술된 목표로 지정

- 매니페스트 파일 : 목표가 명세 된 파일을 지정
  - 생략 시 ‘Makefile’로 지정 (GNU Make)

- 디렉터리 지정 (GNU Make 전용) : -C (디렉터리)
```
예) make all –C hello
```
- 시뮬레이션(touch만 실행) : -t
  - 새로 make를 실행한 것처럼 처리 (재컴파일 X)
```
ex) make 이후 main.c를 변경한 후 make –t 를 실행하면,
    touch main.o diary_exe 를 수행
```

### make 실행
- make

![그림16](https://ji-hun-park.github.io/assets/images/LNXIMG020.jpg "그림16"){: .align-center}

### 부가설명
- Makefile 개선하기
  - 매크로를 사용하여 중복되는 파일 이름을 특정 단어로 치환
  - C언어에서 #define을 하는 것과 비슷한 원리

![그림17](https://ji-hun-park.github.io/assets/images/LNXIMG021.jpg "그림17"){: .align-center}
- 매크로 작성 규칙
  - 매크로를 참조할 때는 소괄호나 중괄호로 둘러싸 앞에 ‘$’를 붙임
  - 탭으로 시작해서는 안되며 ,:,=,#,”” 등은 매크로 이름에 사용불가
  - 매크로는 반드시 치환될 위치보다 먼저 정의되어야 함

- make의 매크로 사용은 유닉스 변수 사용과 동일
  - 정의 : VAR = arguments (공백으로 구분)
  - 사용 : $(VAR)

- 매크로 사용으로 컴파일 옵션 지정도 가능

- make에서 사용하는, 미리 정의된 매크로 확인
  - make –p

## 마무리
이상으로 이번 실습을 마치겠습니다.  
열심히 따라와 주셔서 감사합니다!
