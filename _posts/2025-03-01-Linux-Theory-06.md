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
이 장에서는 먼저 프로그램 레이아웃, 명령줄 인자,  
프로그램 환경 및 종료 핸들러 문제를 다룹니다.  
그런 다음 이 장에서는 프로세스 생성,  
프로세스 파괴 및 데몬 프로세스를 포함한 UNIX 프로세스 모델을 다룹니다.

# 5.0 Program Layout
유닉스 프로그램은 프로그램을 짜고 실행파일을 만들면,  
유닉스 차원에서 부과되는 프로그램의 레이어가 있습니다.  
유닉스는 기본적으로 C언어를 사용합니다.

## main Function
### main Function (1/3)
프로세스에 대해서 설명하기 전에, 프로그램 실행 시  
어떻게 main()함수가 수행 되어지는지 살펴봅시다.  
즉, main함수로 command-line arguments들과 환경변수들이 어떻게 넘어가고,  
메모리 레이아웃이 어떻게 구성되는지 살펴봅시다.

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
프로그램을 실행 시 exec를 호출한 프로세스는 명령줄 인자들을  
새로운 프로그램으로 넘길 수 있다.  
예를 들어 사용자들의 명령을 처리해 주는 셸은 사용자가 입력한 실행프로그램 뒤에  
아규먼트들을 실행프로그램 실행 시 넘겨준다.
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
>특정 환경 변수에 접근하는 것은 일반적으로 **environ** 변수가 아닌  
**getenv** 및 **putenv** 함수를 통해 이루어집니다.

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
윈도우와 달리 대부분의 유닉스 시스템들은  
main함수에 세번째 아규먼트를 환경변수로써 제공한다.  
즉, 호출 프로세스는 프로그램을 실행하기 전에  
실행프로그램의 환경변수를 임의로 지정하는 것이 가능 하다.  
환경변수는 다음 파일들에서 설정가능하다.  
:.profile, .login, .cshrc. 형식은 [변수명:  값]으로 설정한다.

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
맨 앞이 코드 부분 다음이 이니셜라이즈드 데이터, 언이니셜라이즈드 데이터(bss),  
토탈 사이즈(10진수), 다음은 16진수, 마지막 파일명
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

# 5.1 Review of the notation of a process
## Process
### Process (1/2)
- 프로세스란 단순히 실행 중인(순간에) 프로그램의 인스턴스(instance)입니다.
- 프로세스는 프로그램의 실행에 해당(대응)합니다.
  - 프로그램은 컴파일되면 그 내용이 고정이지만, 프로세스는 그 변수의 내용이 매 순간 변한다.
- 프로그램 ≠ 프로세스
  - 프로그램은 그냥 컴파일한 실행 파일.
  - 프로세스는 그 실행 파일이 메모리에 올라와서 실행되고 있는 인스턴스.
  - 둘은 서로 다르다.
- 프로세스 통합(incorporate), 가지고 있는 것
  - 프로그램 코드(텍스트)
  - 프로그램 변수 내의 데이터 값(initialized, bss, stack, heap)
  - 하드웨어 레지스터(CPU)
  - 프로그램 스택
- 프로세스는 **프로세스 ID(pid)**로 식별(대응)됩니다.
- 셸은 새 프로세스를 만듭니다.
- 프로세스는 특정 시스템 콜(fork)에서 만들어줍니다.
  - 프로세스에서 차일드(child, 자식) 프로세스를 만듭니다.

```
 $ cat file1 file2
```
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG047.jpg "그림04"){: .align-center}
이건(명령어) 문자열에 불가한 것이므로 셸이 해석해서 프로세스로 만들어 줍니다(fork() exec 시스템 콜 사용).  
```
 $ ls | wc -l
```
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG048.jpg "그림05"){: .align-center}
ls와 wc를 pipe해서 앞에 있는 프로세스의 출력을 뒤에 있는 프로세스의 입력으로 넣어줍니다.  
각각의 프로세스에는 pid가 있습니다.(셸에도 있음)

### Process (2/2)
**Process environment(프로세스 실행환경)**  
- 유닉스의 모든 프로세스는 어떤 다른 프로세스를 실행시킬 수 있다.
- 이를 통해 UNIX 프로세스 환경은 파일 시스템의 디렉터리 트리와 평행한 계층 구조를 갖게 된다.
  - 시스템 콜 실행 시 프로세스의 자식 프로세스가 만들어져 계층이 생긴다.
  - 여러 개 만들 수 있으므로 계층 트리 구조(hierarchical structure)가 만들어진다, 마치 파일 시스템 구조처럼
- 프로세스 트리의 맨 위 루트에 가장 중요한 단일 제어 프로세스(single controlling process)가 있다.
  - 이는 궁극적으로 모든 시스템 및 사용자 프로세스의 조상인 `init`이라는 매우 중요한 프로그램의 실행이다.
  - 아주 극도로 중요한 프로그램인 <span style="color:red">**init**</span> 시스템 콜(OS의 프로세스)
  - 모든 프로세스의 부모 혹은 조상이 된다(아담)
- UNIX는 프로세스 생성 및 조작을 위한 소수의 시스템 콜을 제공한다.(프로세스와 관련된 시스템 콜)
  - fork, exec, wait, exit 등등

# 5.2 Creation process
## The getpid(2)and getppid(2)system call
```c
/* process_id.c */
#include <unistd.h>

pid_t getpid(void);
//Returns: the process ID of the calling process

pid_t getppid(void);
//Returns: the parent process ID of the calling process
```
getpid와 getppid 시스템 콜은 unistd 헤더를 include 해야합니다.  
getpid의 리턴 값은 시스템 콜을 호출한 프로세스의 프로세스 아이디  
getppid의 리턴 값은 시스템 콜을 호출한 프로세스의 부모 프로세스 아이디

모든 프로세스는 고유한(unique) 프로세스 ID(pid)와  
음이 아닌 정수(non-negative integer)를 갖습니다.  
프로세스 ID는 고유(유일)하지만 재사용 가능합니다.  
(프로세스가 사라지면 다시 가져다 쓸 수 있음)

## The fork(2) system call
### The fork(2) system call (1/4)
```c
#include <unistd.h>

pid_t fork(void);

//Returns: 0 in child, process ID of child in parent, -1 on error
```
호출 프로세스의 추출된 복제본인 새 프로세스를 생성합니다.
```c
pid_t pid;
…
pid = fork();
```
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG049.jpg "그림06"){: .align-center}
차일드(자식) 프로세스를 만들어주는 시스템 콜  
리턴 값이 0이면 만들어진 차일드 프로세스에서 실행되는 것이고,  
0이 아니면 패런트(부모)에서 차일드 PID를 리턴하는 것이다.  
에러일 때는 –1을 리턴한다.

새로운 프로세스를 만들어 주는데 calling 프로세스와 아주 똑같은 프로세스를 만들어 준다.  
(실행하는 코드도 똑같고, 변수들과 그 값이 똑같다, 다만 서로 **별개**의 프로세스다)

### The fork(2) system call (2/4)
- 자식은 부모의 데이터 공간(data space), 힙, 스택의 사본을 얻습니다.
  - 부모 프로세스가 포크(fork)를 실행해 자식 프로세스를 만들면 자식 프로세스는 부모 프로세스의 복제본이다.
  - 부모로부터 변수들의 데이터 공간, 힙, 스택 등을 복사한다.
- 부모와 자식은 이러한 메모리 부분(공간들)을 공유하지 않습니다.
- 부모와 자식은 텍스트 세그먼트(코드 부분)를 공유합니다.(Reentrant, 재진입성)
- 오퍼레이팅 시스템은 시분할 시스템으로
  - 자식을 만들면 프로세스가 늘어나서 순차적으로 실행하지만,
  - 여기선 자식이 부모보다 먼저 실행을 시작하는지, 아니면 그 반대인지는 알 수 없습니다.
  - 이는 커널에서 사용하는 스케줄링 알고리즘에 따라 달라집니다.

### The fork(2) system call (3/4)
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG050.jpg "그림07"){: .align-center}
프로세스A가 패런트(부모) B가 차일드(자식)  
피지컬(물리적) 메모리에 **텍스트 부분**은 패런트와 차일드가 사용하는 부분이 **같다**.  
그러나 **나머지**는 서로 **별개**의 공간을 사용한다.

### The fork(2) system call (4/4)
- pid의 값은 자식과 부모를 구분(distinguishes)합니다.
  - 부모에서 pid: 0이 아닌 양의 정수(non-zero (positive)), 자식의 프로세스 ID
  - 자식에서 pid: 0
- 한계 오류(Limit error, **EAGAIN**)
  - 프로세스 수에 대한 시스템 전체 한도
  - 개별 사용자당 프로세스 수
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG051.jpg "그림08"){: .align-center}
printf로 “One”을 출력한 뒤,  
패런트가 포크(fork)를 실행하면 그 순간 차일드가 복제된다.  
패런트와 차일드 모두 포크를 리턴하는 것부터 시작한다.  
패런트와 차일드의 pid 값은 서로 다르다.  
(차일드는 pid 리턴 값이 0, 부모는 양수)  
차일드의 pid 값이 패런트 pid에 어사인(assign, 할당)된다.

### example
```c
#include <unistd.h>

main()
{
   pid_t pid;
   printf (“Just one process so far\n”);
   printf (“Calling fork …\n”);

   pid = fork();

   if (pid == 0)
      printf(“I’m the child\n”);
   else if (pid > 0)
      printf(“I’m the parent, child has pid %d\n”, pid);
   else
      printf(“Fork returned error code, no child\n”);
}
```
pid_t는 프리미티브 시스템 데이터 타입  
printf를 2번 실행하고 fork한다.

차일드는 리턴 값이 0이고, 패런트는 차일드의 pid 값을 리턴해준다.  
if문으로 pid가 0인 경우를 체크하는데 차일드만 트루다.  
패런트는 pid가 0보다 큰 경우일 때의 이프문을 실행한다.  
이러한 차이점으로 패런트와 차일드가 하는 일을 구분 가능하다.  
음수일 경우 에러다.

# 5.3 Running new program with *exec*
exec는 매우 중요한 시스템 콜로, 새로운 프로그램을 실행시킬 때 fork와 exec를 같이 씁니다.

## The exec family
### The exec family (1/3)
- exec 패밀리는 새 프로그램의 실행을 시작하는 데 사용할 수 있습니다.
- 프로세스 ID는 exec에서 변경되지 않습니다. 새 프로세스가 생성되지 않기 때문입니다.
- exec는 현재 프로세스(텍스트, 데이터, 힙, 스택 세그먼트)를 디스크의 새 프로그램으로 대체할 뿐입니다.
- 많은 UNIX 시스템 구현에서 이 여섯 가지 함수 중 **execve**만이 커널 내의 시스템 콜입니다.

![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG052.jpg "그림09"){: .align-center}
포크로 차일드를 만들면 패런트와 99% 같은 일을 실행하는 프로세스가 생성되지만,  
사용자는 그걸 원하는 것이 아니다.

pid로 패런트가 하는 일과 차일드가 하는 일을 구분할 수는 있다.  
하지만 사용자는 부모와 자식이 전혀 다른 프로그램을 실행해서  
전혀 다른 작업을 하기를 원하는데,  
그 때 실행하는 것이 exec 시스템 콜이다.

위 6가지가 exec의 여러 버전이고, 주로 사용하는 것은 `execve`, 나머지는 라이브러리 형태다.

fork를 실행해서 차일드를 만들면 서로 같은 코드를 실행하는데,  
exec를 실행해서 차일드가 실행하는 코드를 싹 다 바꿔버린다.  
(차일드 프로세스가 스스로 다른 프로그램을 실행하는 코드로 변신한다)

exec는 자기 자신이 하는 일을 바꾸는 것이기에 pid는 바뀌지 않는다.  
(새로운 프로세스가 만들어 지는 것이 아니기 때문에)  
단순히 현재 프로세스가 실행하는 텍스트 코드, 데이터, 힙, 스택을  
새로운 프로그램의 것으로 바꿔준다.

많은 유닉스 시스템에 여러 버전이 있는데(6개), **execve**만이 시스템 콜이고,  
나머지는 라이브러리로 기능은 같고 사용상의 편의 차이만 있다.

### The exec family (2/3)
```c
 int main(int argc, char *argv[]);
```
```c
#include <unistd.h>

int execl(const char *pathname, const char *arg0,.../*NULL*/ );

int execv(const char *pathname, char *const argv[]);

int execle(const char *pathname, const char *arg0,.../*NULL*/,char *const envp[]);

int execve(const char *pathname, char *const argv[], char *const envp[]);

int execlp(const char *filename, const char *arg0,.../*NULL*/ );

int execvp(const char *filename, char *const argv[]);
 
		                //All six return: -1 on error, no return on success
```
- exec는 호출 프로세스와 동시에 실행하기 위해
  - 새로운 하위 프로세스를 생성하지 않는다는 점을 강조하는 것이 중요합니다.
- exec 패밀리의 첫 번째 매개변수는
  - 실행 권한이 있는 실제 프로그램 또는 셸 스크립트를 포함해야 합니다.

6가지 버전이 있고, 아규먼트도 비슷하다.  
4번째(execve)가 메인 시스템 콜

`exec`가 공통으로 들어간다.  
뒤에 l과 p가 붙고, 거기에 추가로 e가 붙는 것이 있고, 추가로 p가 붙는 것이 있다.

패스네임은 실행 프로그램, 실행하면 a.out하고 실행 파일에 아규먼트 들이 붙는데,  
l(list)이 붙은 것 들은 아규먼트들을 일일이 적어주고 마지막에 널이 들어간다.

### The exec family (3/3)
```
PATH 변수에는 경로 접두사(path prefixes)라고 하는 디렉터리 목록이 들어 있으며,
이는 콜론(:)으로 구분되어 있습니다. 예를 들어, name=value 환경 문자열이 있습니다.
 $ echo $PATH 
 PATH=/bin:/usr/bin:/usr/local/bin/:.
```
exec 함수명 뒤의 문자들(접미사)은 구분하는데 다소 도움이 됩니다.

exec 패밀리에서 `l`과 `v`는 앞에 패스네임이 붙는 것은 같은데  
`l`은 아그먼트를 하나 하나 적어주고,  
`v`는 아규먼트가 배열로 들어갑니다.

뒤에 `e`가 붙는 것 들은 뒤에 인바이러먼트 어레이(환경 배열)가 들어가고,  
뒤에 `p`가 붙는 것 들은 맨 앞이 패스네임이 아니라 파일 네임이 들어갑니다.

p는 펑션이 파일네임 아규먼트를 취하며,  
실행 파일을 찾는데 패스 인바이러먼트(PATH 환경 변수)를 사용합니다.  
패스 인바이러먼트는 변수이며, 변수 값을 알려면 앞에 `$`를 붙여야 합니다.

`echo`에 `$PATH`를 주고 실행하면 해당 값이 나온다.  
PATH의 각각의 경로에서 순차적으로 찾는다.(colons, :로 구분)

`l`은 아규먼트(인자)의 리스트(<span style="color:red">**l**</span>ist, 목록)를 나열한다.  
`v`는 나열하지 않고 argv[] 벡터(<span style="color:red">**v**</span>ector)로 묶어준다.  
즉, `l`과 `v`는 상호 배타적이다.  
`e`는 환경변수(<span style="color:red">**e**</span>nvironment variables)의 어레이를(envp[]) 추가한다.

### example (1/3)
```c
/* runls -- ls를 수행하기 위해 “execl”을 수행한다 */

#include <unistd.h>
main(){
	printf("executing ls\n");
	
	execl("/bin/ls", "ls", "-l", (char *)0);
	
	/* 만일 execl이 복귀하면, 호출은 실패한 것이다. 따라서... */
	
	perror("execl failed to run ls");
	exit(1);
}
```
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG053.jpg "그림10"){: .align-center}
l이 붙으면 패스네임이 들어가고, 인자가 리스트로 들어감을 뜻한다.  
인자에서 패스네임 뒤에 나오는 ls는 무시해도 된다.  
`-l`은 옵션  
인자 맨 앞이 실행파일, 끝은 널(NULL)

exec를 실행하는 순간, 이 프로세스가 ls를 실행하는 프로세스가 된다.  
(프로세스는 변하지 않고 프로그램만 변화한다)  
ls를 실행 성공하면 다시 돌아오지 않는다.  
실패했다면(복귀했다면) 밑의 코드를 실행한다.

### example (2/3)
```c
/* runls2 -- ls를 수행하기 위해 execv를 사용한다. */ 
#include <unistd.h>

main()
{
	char *const av[]={"ls", "-l", (char *)0};
	
	execv("/bin/ls", av);
	
	/* 반복하거니와 여기까지 수행이 진행되면 오류를 의미 */
	perror("execv failed");
	exit(1);
}
```
아까는 아규먼트들을 전부 나열했지만 이번엔  
v가 붙으니 어레이(vector)로 하나로 묶었다.

### example (3/3)
**exec로 전달된 인수에 접근하기**  
```c
main()
{
	char * const argin[] = {"myecho", "hello", "world", (char *)0};
	
	execvp(argin[0],argin);
}
```
```c
/* myecho -- 명령줄 인자를 그대로 출력한다 */
main(int argc, char** argv)
{
	while( --argc > 0 ) // 파일명 제외
		printf("%s ", *++argv); // 파일명 이후를 나타냄
		
	printf("\n");
}
```
```
 $ myecho hello world
```
v는 벡터(vector), argv[]  
p가 들어갔으면 파일 네임을 넣는다.  
argc는 3(인자의 개수)  
argv[0]는 파일명(myecho)  
argv[1]은 hello의 대한 포인터  
argv[2]는 world에 대한 포인터  
(char *)0는 NULL

# 5.4 Using *exec* and *fork* together
## example
```c
/* runls3 -- 부프로세스에서 ls를 수행한다 */
#include <unistd.h>

int fatal(char *s){
   perror(s);
   exit(1);
}

main(){
   pid_t pid;
   switch (pid = fork()){
   case -1:
      fatal("fork failed");
   case 0:
      /*자식이 exec을 호출 */
      execl("/bin/ls", "ls", "-l", (char*)0);
      fatal("exec failed");
      break;
   default:
      /* 부모가 자식이 끝날 때까지 수행을 일시 중단하기 위해 wait을 호출 */
      wait((int*)0);
      printf("ls completed\n");
      exit(0);
   }	
}
```
메인 함수에 변수 pid가 있고,  
fork해서 차일드(자식)를 만들고  
리턴 값이 음수면 실패.

차일드는 포크의 리턴 값이 0이니 case 0 부분을 실행  
패런트는 디폴트 부분 실행

execl(이그씨엘, 엘이니까 리스트로 나열하고, 패스네임을 준다, ls는 중복된다)

이 때, 차일드는 `ls -l`을 실행하는 프로그램으로 변신한다.  
실패하면(다시 돌아오니) 바로 밑에 있는 코드를 실행한다.(fatal)

fatal은 괄호 안의 해당 문자를 받아서 출력해주고,  
세팅된 에러 넘버의 설명문을 출력한 후 종료한다.

패런트는 wait 시스템 콜을 실행(기다린다)한다.  
자식이 끝날 때까지 기다리고 다음 코드를 실행한다.
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG054.jpg "그림11"){: .align-center}
A가 parent(패런트, 부모), B가 child(차일드, 자식) 프로세스

패런트가 포크 실행, 본인은 차일드의 pid를 리턴, 차일드는 pid 0을 리턴  
pid가 0이면 `execl` 실행해서 `ls`를 실행하는 코드로 바뀌고 `ls -l`을 실행  
패런트는 자식이 끌날 때까지 기다리다가, 자식이 종료되면 깨어난다.

## The docommand example
```c
/* docommand --run shell command, first version */
int docommand(char *command)
{
    pid_t pid;
    if((pid = fork()) < 0 )
    	return (-1);
    
    if(pid==0) /* child */
    {
    	execl("/bin/sh", "sh", "-c", command, (char *)0);
    	perror("execl");
    	exit(1);
    }
    
    /* code for parent */
    /* wait until child exits */
    wait((int *)0);
    return(0);
}
```
- 셸 호출에 사용된 -c 인자는 표준 입력이 아닌 다음 문자열 인자에서 명령을 가져오도록 지시합니다.

cmd를 실행하면 셸이 패런트가 된다.  
포크해서 차일드를 만들면 차일드도 셸을 실행하지만,  
커멘드를 실행하는걸 원한다.  

위 코드는 자식 프로세스를 exec로 커멘드를 실행하는 프로그램으로 바꿔주는 과정이다.

cmd가 문자열을 받았을 때 문자열이 커멘드로 들어오면  
이걸 파싱해야 하는데 이게 복잡하다.(생략)

포크를 실행하고 리턴 값이 음수면 포크에 실패했으니 -1을 리턴하고,  
성공했다면 pid가 0이면 자식이니,  
`execl`로 입력한 커멘드를 셸을 실행하여  
`-c` 옵션으로 차일드를 하나 더 만들어 실행한다.  
부모는 wait한 뒤 종료한다.

# 5.5 Inherited data and file descriptors
포크해서 만들어진 차일드는 패런트의 모든 속성이나 데이터를 상속 받는다.

## fork, files and data
**부모 프로세스에서 열려(open) 있는 모든 파일은 자식 프로세스에서도 열려(open) 있습니다.**  
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG055.jpg "그림12"){: .align-center}
- 패런트가 오픈한 모든 파일 들은 차일드에서도 오픈됩니다.
  - 오프셋 마저도 똑같습니다.
  - 차일드가 출력하면 패런트 오프셋도 똑같습니다.
- 부모와 자식이 **동일한 파일 오프셋을 공유**하는 것이 중요합니다.
- 자식이 표준 출력에 쓸(writes) 때 부모의 오프셋이 자식에 의해 업데이트됩니다.

### example
```c
/* fork_filedes.c */
/* fork는 "data"가 최소한 20문자 이상임을 가정한다 */

main(){
   int fd;
   pid_t pid;
   char buf[10];  /* 파일 자료를 저장할 버퍼 */
   if((fd = open("data", O_RDONLY)) == -1)
      fatal("open failed");
   
   read(fd, buf, 10);   /* 파일 포인터를 전진 */
   printpos("Before fork", fd);
   
   switch(pid = fork()) {
   case -1:    /* 오류 */
      fatal("fork failed");
      break;
   case 0:     /* 자식 */
      printpos("Child before read", fd);
      read(fd, buf, 10);
      printpos("Child after read", fd);
      break;
   default:    /* 부모 */
      wait((int *)0);
      printpos("Parent after wait", fd);
   }
}
```
포크로 만들어진 차일드는 패런트가 오픈한 파일을 그대로 오픈해서 공유한다.  
패런트가 리드 라이트하면서 변화된 오프셋도 차일드가 그대로 가진다.  

패런트와 차일드가 가리키는 파일 테이블이 동일하기 때문에,  
패런트와 차일드는 파일 오프셋을 서로 공유하게 된다.  
리드온리 모드로 data 파일을 오픈하고 패런트가 10바이트만큼 리드한다.

프린트포스(printpos) 펑션  
엘시크(lseek)를 사용해서 파일 디스크립터(서술자)에  
POS = lseek(fd, 0, seekcir)를 리턴해준다(현재 파일 오프셋),  
여기에선 10이 출력 된다.

포크하면 pid에 차일드 pid가 들어가고, -1이면 페이탈 실행  
pid가 0이면 자식, 디폴트는 부모

차일드가 프린트포스 실행하면 패런트의 오프셋인 10이 찍힌다,  
그리고 차일드가 리드 10을 하면,
프린트 포스했을 때 20이 찍히고 종료된다.  
부모가 기다렸다, 깨어나서 프린트포스를 실행하면 20이 출력된다.

## Inherited properties from parent process
**fork (2)**  
**부모 프로세스에서 상속된 속성들**  
- 실제(Real) 사용자(user) ID, 실제 그룹(group) ID
- 유효(effective) 사용자 ID, 유효 그룹 ID
- 보충(Supplementary) 그룹 ID
- 프로세스(Process) 그룹 ID
- 세션(Session) ID
- 제어 터미널(Controlling terminal)
- set-user-ID 및 set-group-ID 플래그(flags)
- 현재 작업 디렉터리(Current working directory)
- 루트 디렉터리(Root directory)
- 파일 모드 생성 마스크(File mode creation mask)
- 신호 마스크 및 처리(Signal mask and dispositions)
- 열려 있는 모든 파일 서술자(FD)에 대한 close-on-exec 플래그
- 환경(Environment)
- 첨부된 공유 메모리 세그먼트(Attached shared memory segments)
- 메모리 매핑(Memory mappings)
- 리소스 제한(Resource limits)

위는 포크로 자식이 부모로부터 상속받는 프라퍼티들(거의 대부분)이다.

## The difference between the parent and child

패런트 프로세스와 차일드 프로세스의 다른 값들은  
포크하면서 리턴되는 pid 값(부모는 자식의 pid, 자식은 0),  
각각의 프로세스 ID(pid) 값,  
두 프로세스의 부모 프로세스 ID가 다르다.  
자식의 부모 프로세스 ID는 fork를 실행한 프로세스의 pid가 되지만,  
부모의 부모 프로세스 ID(ppid)는 변경되지 않는다.

자식은 tms_utime, tms_stime, tms_cutime, tms_cstime 등이  
값이 0으로 세팅된다(프로세스의 실행 시간을 측정하는 변수들),  
파일록(File locks)도 상속 받지않고,  
팬딩(pending)된 알람(alarm)도 클리어(clear) 된다.  
팬딩 신호(pending signals)가 empty set으로 set된다.
```
clock_t tms_utime;  /* 포르세스의 사용자 시간 */
clock_t tms_stime;  /* 프로세스의 커널 시간 */
clock_t tms_cutime; /* 자식들의 사용자 시간 */
clock_t tms_cstime; /* 자식들의 커널 시간 */
```

## exec(fork()) and open files
원래의 프로그램에서 열린(opened) 파일은 `exec`를 통해  
완전히 새로운 프로그램이 시작될 때 **열린 상태로 유지**됩니다.  
이러한 파일에 대한 읽기-쓰기 포인터(파일 오프셋)는 **`exec` 호출에 의해 변경되지 않습니다.**  
`close-on-exec` 플래그가 켜져 있으면(기본값은 꺼짐),  
파일 조각(fragment)은 `close-on-exec` 플래그가 활성화되는(enabled) 방식을 보여줍니다.  
```c
/* close_on_exec.c */

#inlucde <fcntl.h>
.
.
int fd;
fd = open(“file”, O_RDONLY);
.
.
fcntl(fd, F_SETFD, 1);	/* close-on-exec 플래그를 on으로 설정 */

fcntl(fd, F_SETFD, 0);	/* close-on-exec 플래그를 off로 설정 */

res = fcntl(fd, F_GETFD, 0);	/* res==1:on, res==0:off */
```
포크하면 차일드는 패런트가 오픈한 fd를 그대로 상속받는다.  
파일이 패런트와 차일드를 실행한 프로세스에서 오픈이 안 되어 있더라도 오픈된다.  
파일 오프셋이 차일드가 exec해도 바뀌지 않고 그대로다.

자식이 부모의 fd를 그대로 상속받는 것을 해결하기 위한 것이 close-on-exec 플래그다.

클로즈 온 이그씨 플래그는 차일드가 exec을 실행하면 패런트가 오픈한 파일을 자동으로 exit한다.  
기본값은 off로 설정되어 있어, 자동으로 close가 안 된다.  
on하면 패런트가 오픈한 파일 디스크립터를 자동으로 close한다.  
위는 이를 위해 플래그를 on해주는 예제다.

파일컨트롤(fcntl)을 이용해 플래그를 on(1)할 수 있다, 0이면 off  
포크하고 차일드가 exec하면 자동으로 부모의 fd는 close된다.

### example
```c
/* close_off_exec.c */

int main(int argc, char** argv)
{
  int fd;
  pid_t pid;
	
  fd = open(“test”, O_RDONLY);
	
  pid = fork();
  
  if(pid == 0){
    execl("exec","child",0);
  }
  if(pid < 0){
    perror("MAIN PROCESS");
  }
	
  wait(NULL);
  printf("MAIN PROCESS EXIT\n");
  close(fd);

  return 0;
}
```
```c
/* exec.c */

int main(int argc, char** argv)
{
  if( read(3, buff, 512) < 0){
    perror("EXEC PROCESS");
    exit(1);
  }
	
  printf("EXEC PROCESS EXIT\n");
	
  return 0;
}
```
`test` 파일을 오픈하고, 차일드를 만들고(fork),  
차일드일 경우, execl을 실행해서 exec.c를 실행한다(아래 코드가 exec.c),  
차일드가 실행하는동안 패런트는 기다리고,  
자식은 argc가 1이다, argv에는 `child`만 있다.  
부모의 fd가 3인데, 자식이 바로 fd 3을 리드하면,  
부모가 오픈한 파일이 그대로 리드된다.(파일 오프셋이 바뀐다)

## Inherited properties from the calling process
**exec(2)**  
**호출 프로세스에서 상속된 속성들**  
- Real user ID and real group ID
- Supplementary group IDs
- Process group ID
- Session ID
- Controlling terminal
- Time left until alarm clock
- Current working directory
- Root directory
- File mode creation mask
- File locks
- Process signal mask
- Pending signals
- Resource limits
- Values for tms_utime, tms_stime, tms_cutime, and tms_cstime

자기 자신이 변신하더라도 상속 받는 것은 대동소이하다.

## 마무리
이상으로 Linux 이론의 프로세스1편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
