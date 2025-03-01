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
- `environmentp`는 캐릭터 문자열의 어레이, 프로세스가 실행되는 환경 변수
- $env 명령어를 실행하면 환경 변수가 쭉 나타난다, 굉장히 많음
- 위 이미지는 대표적 환경 변수와 그 값
- 여기에서 명령어를 찾기 때문에 환경 변수에 없으면 명령어를 못 찾아서 실행이 안 됨
- 패스에 :.이 없기 때문에 현재 디렉터리에서 바로 실행 불가
- 패스네임을 문자열로해서 `envp`에 저장
- 이게 없어도 `stdlib.h`를 include하면 Default로 되어 있음

환경변수 리스트는 시스템의 환경 변수들을 말한다.  
즉, HOME, PATH.. 등등. 시스템 사용에 필요한 정보들을 말한다.  
환경변수는 global변수로 정의되어 있다.(extern 키워드 사용)  
환경변수들에 접근을 용이하게 하기 위해 `getenv`와 `putenv`함수를 제공하고 있다.  
윈도우와 달리 대부분의 유닉스 시스템들은 main함수에 세번째 아규먼트를 환경변수로써 제공한다.  
즉, 호출 프로세스는 프로그램을 실행하기 전에 실행프로그램의 환경변수를 임의로 지정하는 것이 가능 하다.  
환경변수는 다음 파일들에서 설정가능하다 :.profile, .login, .cshrc. 형식은 [변수명:  값]으로 설정한다.

## Memory Layout a C Program
### Memory Layout a C Program (1/3)
**C 프로그램은 다음으로 구성됩니다.**  
- 텍스트(코드) 세그먼트(Text(Code) Segment):
  - 기계어 코드, 실행할 프로그램 명령어(머신 명령어, machine instruction)가 실행되는 부분
- 초기화된 데이터 세그먼트(Initialized data segment):
  - 초기화된 전역(global) 및 정적(static) 변수 들이 메모리에 확보하는 부분
```c
 int maxcount = 99
```
- 초기화되지 않은 데이터 세그먼트(Uninitialized data segment, bss):
  - 초기화되지 않은 전역 및 정적 변수 들이 메모리에 확보하는 부분.
  - 이 세그먼트의 데이터는 프로그램이 실행되기 전에 커널에 의해 산술 0 또는 널 포인터로 초기화된다.
```c
 long sum[1000]; 
```
- 스택(Stack):
  - 함수에 로컬 변수를 저장한다.
  - 함수에 지역 변수들이 많은데, 함수가 호출되면 자동 변수(automatic variables)가 스택 맨 위에 할당(allocated)된다.
  - 함수가 종료(return)되면 지역 변수들을 더 이상 쓰지 않으니(로컬 변수들은 펑션 안에서만 유효하다) 변수가 할당 해제된다.(메모리 확보, de-allocated)

C프로그램을 실행하면 실행 파일이 나오는데,  
이 것이 메모리에 로드될 때 메모리에 어떤 구조를 가지고 메모리를 차지하는 지를 나타낸다.

C프로그램은 크게 4개의 영역(text, global, stack, heap)으로 메모리가 레이아웃된다.  
여기서 global영역은 초기화 여부에 따라 initialized data segment와 uninitialized data segment(bss-block started by symbol)로 나뉜다.  
initialized data segment의 경우 컴파일된 실행모듈의 크기에 포함되지만 uninitialized data segment의 경우 실행모듈의 크기에 포함되지 않는다.  
uninitialized data segment는 프로그램 실행 시 커널에의해 공간이 할당되고 초기화 되어진다.

### Memory Layout a C Program (2/3)
**Heap**  
프로그램을 실행할 때 **동적(Dynamic)** 메모리를 할당하면 `힙(Heap)`에 들어간다.

![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG046.jpg "그림03"){: .align-center}
- 초기화되지 않은 데이터 세그먼트의 내용은 디스크의 프로그램 파일에 저장되지 않습니다.
- 프로그램 파일에 저장해야 하는 프로그램의 유일한 부분은 텍스트 세그먼트와 초기화된 데이터입니다.

```
$ size /usr/bin/cc /bin/sh
 text    data   bss    dec     hex    filename
 79606   1536   916    82058   1408a  /usr/bin/cc
 619234  21120  18260  658614  a0cb6  /bin/sh
```
bin 안에 있는 cc와 sh의 사이즈를 비교하는 명령어를 실행한다.  
맨 앞이 코드 부분 다음이 이니셜라이즈드 데이터, 언이니셜라이즈드 데이터(bss), 토탈 사이즈(10진수), 다음은 16진수, 마지막 파일명
```c
 int *x;
 x[11];
 x = (int *)malloc(sizeof(int),100)
``` 
이렇게 동적 메모리 할당을 해야 x[11]을 쓸 수 있다.  
문자를 저장하고 초깃값이 있는 정적 변수 값을 저장한다.

초깃값이 없는 정적 변수 값을 저장하고 동적 변수들을 저장한다(힙과 스택이 늘었다 줄었다 한다).  
이 중 앞에 텍스트와 이니셜라이즈드 데이터만 디스크 실행 파일에 저장하면 된다.

### Memory Layout a C Program (3/3)
```c
/* init.c */
int myarray[50000]={0};
int main(){
   return 0;
}
```
```c
/* noninit.c */
int myarray[50000];
int main(){
   return 0;
}
```
```
[root@localhost root]# cc noninit.c -o noninit 
[root@localhost root]# cc init.c -o init
[root@localhost root]# ls -l init noninit
-rwxr-xr-x    1 root     root       214604  4월  7 17:00 init
-rwxr-xr-x    1 root     root        14598  4월  7 17:00 noninit
[root@localhost root]# size init noninit
   text    data     bss     dec     hex  filename
    706  200272       4  200982   31116  init
    706     252  200032  200990   3111e  noninit
```
앞은 이니셜 라이즈(초기화)한 것 뒤는 안 한 것(안 한 건 bss에 들어간다).  
앞에 것은 디스크에 저장, 뒤는 저장 안 한다.  
디스크에 저장되면 사이즈가 크고, 아니면 사이즈가 작다(텍스트는 같다).  
뒤에 건 bss 부분이 크지만 실제로 데이터에 저장이 안 된다.  
합쳐서 나오는 토탈 사이즈는 실제 사이즈가 아님.

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
