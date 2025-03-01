---
title:  "Linux 기초 이론 06 PROCESS1"
excerpt: "Linux 기초 이론 여섯 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-03-01T24:00:00-00:00
---

# Linux Lecture Theory 06 PROCESS1
## Introduction
Process(프로세스)는 대부분 운영 체제 모델에서 기본적인 활성 엔티티입니다.  
이 장에서는 먼저 프로그램 레이아웃, 명령줄 인자, 프로그램 환경 및 종료 핸들러 문제를 다룹니다.  
그런 다음 이 장에서는 프로세스 생성, 프로세스 파괴 및 데몬 프로세스를 포함한 UNIX 프로세스 모델을 다룹니다.

# 5.0 Program Layout
유닉스 프로그램은 프로그램을 짜고 실행파일을 만들면,  
유닉스 차원에서 부과되는 프로그램의 레이어가 있습니다.  
유닉스는 기본적으로 C언어를 사용합니다.

## main Function
### main Function (1/3)
프로세스에 대해서 설명하기 전에, 프로그램 실행 시 어떻게 main()함수가 수행 되어지는지 살펴봅시다.  
즉, main함수로 command-line arguments들과 환경변수들이 어떻게 넘어가고, 메모리 레이아웃이 어떻게 구성되는지 살펴봅시다.

**Main function’s prototype**  
C언어는 기본적으로 메인(main) 펑션(function)으로 시작한다.  
```c
 int main(int argc, char *argv[]);
```
- **인자들**  
  - argc: 명령어(command-line)를 공백으로 구분해놓은(공백을 통해 나눠놓은) 인자 수
  - argv: 각각의 인자가 들어있는 포인터들의 배열(**argv도 가능)

- exec(굉장히 중요한 시스템 콜): 새로운 프로세스를 만들기 위해 반드시 필요
- 메인이 실행되기 전에 실행되는 특별한 스타트 업 코드가 있다.
- C 프로그램이 exec 함수 중 하나에 의해 커널에 의해 실행될 때, 메인 함수가 호출되기 전에 특별한 시작 루틴(start-up routine)이 호출됨.
- 실행프로그램들은 이 start-up루틴을 프로그램의 starting address로서 명시한다.
- start-up루틴은 커널로 부터 위과 같은 두 가지 값들을(인자들을) 취한다.

```
 user`s shell ->(호출) start up code exit(main(...)); 이후 메인 호출
```
- 실행 가능한 프로그램 파일은 이 루틴을 프로그램의 시작 주소로 지정한다.
- 프로그램을 실행할 때 실행 프로그램 파일이 메인이 아닌 스폐셜 스타트 업 코드에서 실행하게 되어있다(셸이 실행함).
- 이런 처리는 커널에서 알아서 실행해준다.
- 스타트 업 루틴에서 하는 건 **커멘드라인 아규먼트 처리**(argcv)와 **환경 설정**

>만약 C로 짜 놓은 프로세스 안에서 호출될 경우 기존 프로세스는 죽을 것이다.

![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG044.jpg "그림01"){: .align-center}

### main Function (2/3)
**Command-Line Argument**  
프로그램을 실행 시 exec를 호출한 프로세스는 명령줄 인자들을 새로운 프로그램으로 넘길 수 있다.  
예를 들어 사용자들의 명령을 처리해 주는 셸은 사용자가 입력한 실행프로그램 뒤에 아규먼트들을 실행프로그램 실행 시 넘겨준다.
```c
 $ ./echoarg arg1 TEST foo
 argv[0]: ./echoarg
 argv[1]: arg1
 argv[2]: TEST
 argv[3]: foo
```
```c
#include <stdio.h>

int main(int argc, char *argv[]) 
{ 
   int i; 
   for (i = 0; i < argc; i++) /* echo all command-line args */ 
      printf("argv[%d]: %s\n", i, argv[i]); 
   exit(0); 
}
```
실행파일을 실행하고 아규먼트(인자)를 집어 넣으면  
argc = 4고, 각각의 문자열이 배열에 들어간다.  
각각의 argv 자체가 캐릭터(문자)의 포인터이고,  
배열 역시 포인터이기 때문에 char **argv;로 선언된다.  
이 것 들을 스타트업에서 선언해 준다.

환경 변수에 어떤 명령을 입력하면, 명령을 찾는 디렉터리의 순서를 패스 디렉터리가 세팅해 준다.  
커런트 워킹 디렉터리(cwd)가 패스에 설정이 안 되어 있으면 명령을 못 찾기에  
`./`로 상대 패스네임을 넣어준다.  

argc: 배열 크기  
argv: 각각의 문자열 들의 배열

### main Function (3/3)
```
 $ env
 $ echo $HOME
 (.profile, .login, .cshrc)
```
**Environment List**  
- `환경 목록`은 문자 포인터의 배열입니다.
- 포인터 배열의 주소는 전역 변수 `environ`에 포함되어 있습니다.

```c
 extern char **environ; 
```
```c
extern char **environ;	/* stdlib.h */
for (i = 0; environ[i]; i++)
  printf(“env[%d]: %s\n", i, environ[i]);
```
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG045.jpg "그림02"){: .align-center}
>특정 환경 변수에 접근하는 것은 일반적으로 **environ** 변수가 아닌 **getenv** 및 **putenv** 함수를 통해 이루어집니다.

- 대부분의 UNIX 시스템은 main 함수에 세 번째 인자를 제공합니다.
```c
 int main(int argc, char *argv[], char *envp[]); 
```

![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG046.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG047.jpg "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG048.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG049.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG050.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG051.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG052.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG053.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG054.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG055.jpg "그림12"){: .align-center}

## 작성중

## 마무리
이상으로 Linux 이론의 프로세스1편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
