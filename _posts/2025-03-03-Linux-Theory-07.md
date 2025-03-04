---
title:  "Linux 기초 이론 07 PROCESS2"
excerpt: "Linux 기초 이론 일곱 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-03-03T24:00:00-00:00
---

# Linux Lecture Theory 07 PROCESS2
## Introduction
Process(프로세스)는 대부분 운영 체제 모델에서 기본적인 활성 엔티티입니다.  
이 장에서는 먼저 프로그램 레이아웃, 명령줄 인자,  
프로그램 환경 및 종료 핸들러 문제를 다룹니다.  
그런 다음 이번 장에서는 프로세스 생성,  
프로세스 파괴 및 데몬 프로세스를 포함한 UNIX 프로세스 모델을 다룹니다.

# 5.6 Terminating processes with the *exit* system call
## Process Termination
프로세스가 종료되는 방법은 8가지가 있습니다.  
프로세스 종료는 크게 Normal 과 Abnormal termination으로 나뉩니다.

Normal termination은 main 프로그램에서 리턴해서  
최종적으로 _exit()함수를 호출하고 종료합니다.

Abnormal termination은 _exit()에 의해서 종료되지 않는 경우를 말합니다.  
Abort(시스템 콜)를 사용해 종료하는 경우로,  
코어(메모리에 있는 모든 변수, 스택, 힙 등) 덤프합니다.

스레드와 관련이 있으며,  
주로 시그널과 관련한 종료로 볼 수 있습니다.

정상적인 종료는 5가지 방법으로 발생합니다.  
- main에서 복귀
- exit 호출
- _exit 또는 _Exit 호출
- 마지막 스레드가 시작 루틴에서 복귀
- 마지막 스레드에서 pthread _exit 호출

비정상적인 종료는 3가지 방법으로 발생합니다.  
- abort 호출
- 신호(signal) 수신
- 마지막 스레드가 취소 요청에 응답

## The exit(3) system call
프로세스를 터미네이트하는 엑시트 시스템 콜과 라이브러리  
```c
#include <stdlib.h>

void exit(int status);
void _Exit(int status);
```
```c
#include <unistd.h>

void _exit(int status);
```
위는 라이브러리로, `stdlib` 라이브러리에 있다, 아래는 시스템 콜  
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG056.jpg "그림01")

exit에 들어가는 status 아규먼트(인자)는 프로세스의 exit status 데이터다.  
종료되는 상태를 이 변수(int status)에 저장한다.  
(자동으로 해주는 것도 있고, 일부는 유저가 넣어준다.)

integer니까 4바이트, 로우 오더(low order, 낮은 순위)의  
8비트(1바이트)는 패런트(부모)에 전달이 된다.  
이 하위 8비트는 부모에서 사용할 수 있는데, wait 시스템 콜에 쓰인다.

차일드(자식) 프로세스가 죽기 전까지 패런트(부모)는 wait해서 기다리는데,  
wait에는 `&status`가 있다.  
차일드가 exit(status)하면 이 스테이터스(상태)가 부모에게 전달이 된다.

_exit 및 _Exit는 커널로 즉시 돌아간다.

convention(컨벤션, 협약)해서 프로그램이 종료될 때,  
어떤 리턴 또는 엑시트로 종료하는데, 그에 대한 벨류가 있다.  
이 벨류(value)에 대한 규약이 있는데,  
관례에 따라 정상적으로 종료되면 0을 return하고,  
문제가 있었으면(발생했으면) 0이 아닌 값을 전달한다.

return 0 해도 스타트 업 코드에서 exit로 main의 return 값을 받기에 똑같다.  
(exit(0)은 main()의 return(0)와 동일하다.)

앞에 _가 있으면 종료하는 것 외에 추가 작업(cleanup 작업, 정리 처리)을 한다.  
(오픈(open) 했는데 클로즈(close) 안 한 것들을 닫아주고  
(표준 I/O 라이브러리의 정리 종료(fclose))  
메모리 알로케이트(allocate, 할당)한 것들 프리(free)해주고 엑시트(exit)한다.)

## The atexit(3)
엣엑시트 라이브러리  
```c
#include <stdlib.h>

int atexit(void (*func)(void));
 
//Returns: 0 if OK, nonzero on error
```
- ISO C를 사용하면 프로세스는 exit에 의해 자동으로 호출되는 최대 32개의 함수를 등록할 수 있습니다.
- 이를 종료 핸들러라고 하며 atexit 함수를 호출하여 등록합니다.
- 종료(exit) 함수는 등록 순서의 역순으로 이러한 함수를 호출합니다.
- 지정된 플랫폼에서 지원하는 종료 핸들러의 최대 수
  - sysconf(_SC_ATEXIT_MAX);

(*func)에 포인터를 넣어서 exit가 실행되면  
클린업(cleanup)을 먼저하고 엑시트를 실행하는데,  
이 클린업에 엑시트 핸들러(handlers)를 등록한다.  
이는 등록된 역순으로 실행되는데, 최대 32개까지만 등록이 가능하다.

일종의 콜백 함수(callback function)다.

## Exit procedure diagram
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG057.jpg "그림02"){: .align-center}
메인 프로그램이 있으면, main 함수 실행 전에 스타트 업 코드가 실행이 된다.  
유저가 exit를 실행하면 cleanup을 실행하고 최종적으로 exit해서 죽는다.  
메인 프로그램에서 exit해도 죽고, function으로 call해서 exit해도 죽는다.

start up code에서 main을 호출해서 main 실행하고 끝나서 return하면 자동적으로 exit해서 끝난다.  
엑시트는 등록된 기본 클린 업이 있고 엑시트 핸들러들이 있다(32개까지).  
등록된 역순으로 가장 최근에 추가된 핸들러에서 standard 순으로 실행하고 끝나면 exit.  
유저 펑션(함수)이나 메인 펑션에서 `_exit`하면 곧바로 들어간다(클린업을 하지 않는다).

### example
```c
void func1() { printf("print func1\n"); }
void func2() { printf("print func2\n"); }
void func3() { printf("print func3\n"); }
void func4() { printf("print func4\n"); }

int main(int argc, char** argv){
	pid_t pid;
	
	atexit(func1);	atexit(func2);
	atexit(func3);	atexit(func4);

	pid=fork();
	
	if(pid==0){
		printf("CHILD PROCESS : CALLED\n");
		printf("CHILD PROCESS : EXIT CALL\n");
		exit(0);
	}	
	if(pid < 0){
		perror("PARENT ERROR");
		exit(1);
	}
	wait(NULL);
	printf("PARENT PROCESS : EXIT CALL\n");
	exit(0);
}
```
```
[root@localhost unix]# ./a.out
CHILD PROCESS : CALLED
CHILD PROCESS : EXIT CALL
print func4
print func3
print func2
print func1
PARENT PROCESS : EXIT CALL
print func4
print func3
print func2
print func1
```
출력하는 함수 4개 만들고 엣엑시트로 핸들러를 등록한다.  
포크로 차일드 만들고 차일드가 출력부터하고,  
엑시트하면 등록된 핸들러가 역순으로 실행된다.  
끝나면 패런트가 깨어나서 출력하고,  
엑시트하면 핸들러를 또 역순으로 실행한다.

# 5.7 Synchronizing process
싱크로나이징 프로세스  
fork(), exec(), wait()

\$셸이 이 프람트(prompt)를 실행한다.  
커멘드를 입력하면 셸이 이 커멘드를 파싱한다.  
(공백에 따라 구분해 각각을 배열에 넣는다.)

이러면 셸이 fork하고 차일드가 만들어지고,  
차일드가 exec해서 변신한 다음 나중에 exit하고,  
패런트는 그동안 wait하고 다음 명령을 할 수 있게 $를 출력해준다.

## Synchronizing with children
차일드의 싱크로나이징  
프로세스가 종료될 때(정상이든 비정상이든) 패런트가 깨어난다.  
커널은 차일드 프로세스의 모든 열린 파일 디스크립터(서술자)를 종료한다.  
차일드가 사용중이던 모든 메모리를 릴리즈(releases, 해제)한다.

커널은 차일드가 종료한 프로세스의 프로세스 엔트리 중에서  
차일드 pid, exit의 제거 상태, 스테이터스(status) 값 등,  
그리고 cpu 시간이 클로즈(close)되어야 하는데,  
스테이터스를 패런트에 보내야 해서 기다리는 시간이 있다.

그렇기에 패런트가 차일드 프로세스가 터미네이트(종료) 되었다는  
정보를 확인하기까지는 프로세스 엔트리의 정보가 남아있다(스테이터스가).  
종료하는 프로세스의 부모는 wait() 시스템 콜을 통해 이 정보를 확인한다.

컨펌(confirms)은 wait를 통해 스테이터스 값을 전달 받을 때까지,  
패런트가 wait를 해줘야 자식 프로세스의  
프로세스 엔트리가 프로세스 테이블에서 클리어(clear)된다.

## The wait(2) system call
### The wait(2) system call (1/2)
```c
#include <sys/wait.h>

pid_t wait(int *statloc);

//Return: child process ID if OK, 0 (see later), or -1 on error
```
wait 시스템 콜을 쓰기 위해선  
sys(시스템)의 wait header를 include해야 한다.

인티져 포인트(int *)로 변수를 주는 이유는  
차일드가 엑시트해주는 값을 받기 위해서다.

리턴 값은 성공하면 방금 종료한 차일드의 PID, 실패하면 -1,  
0은 나중에 설명하겠다.

웨이트(wait)는 차일드가 러닝(running, 실행)되는 동안 패런트(호출) 프로세스를 잠시 중단(blocking)하고,  
자식이 종료(완료)되면 패런트는 웨이트(wait, 대기)에서 깨어나 다시 시작한다.  
차일드가 1개 초과(두 개 이상)일 경우,  
자식 중 어느 하나라도 종료되면(wait 반환) 깨어난다.

만약 웨이트가 (pid_t)-1을 리턴한다면, 패런트가 만든 차일드가 없는 경우다.  
`errno` = **ECHILD**

차일드가 여러 개면 웨이트를 여러번하면 된다.  
부모 프로세스는 각 자식(offspring)을 기다리며, 루프(loop)에 빠져 있을 수 있다.

### The wait(2) system call (2/2)
```c
int stat;
for(int i=0; i<3; i++){
 	if(fork()==0) {
    	sleep(rand()%100);
		exit(i);
  }
}

 wait(&stat);
 wait(&stat);
 wait(&stat);
```
```c
 while(wait(NULL)!=-1);
```
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG058.jpg "그림03"){: .align-center}
stat은 wait에 들어가는 변수다.  
for를 i 0부터 2까지 실행한다.  
포크해서 리턴 값이 0이면 sleep(차일드만 실행한다.)  
부모는 포크만 3번 실행하고,  
차일드는 곧바로 exit하기에 for 루프로 들어가지 않는다.

자식이 3명이니까 웨이트를 3번 실행한다.  
슬립 시간을 랜덤하게 줬기에 누가 먼저 끝날지는 알 수 없다.  
먼저 종료되는 순서대로 스테이터스가 들어간다.

while 문의 경우 위의 코드의 wait 3번 실행과 비슷한데 우선,  
wait의 인자가 NULL이면 스테이터스 값을 받지 않겠다는 것이고,  
wait의 return 값으로 차일드의 pid 값을 받는데,  
이 과정(wait하다 꺠어나기)을 반복하다, 자식이 없으면 -1을 리턴한다.  
즉, 와일 루프에서 빠져나온다.

### example (1/2)
```c
int status;
pid_t cpid;

cpid = fork();  /* create new process */
if(cpid == 0){
   /* child */
   /* do something ... */
} else {
   /* parent, so wait for child */
   cpid = wait(&status);
   printf(“The child %d is dead\n”, cpid);
}
------------------------------------------------------------------
pid_t pid;
int status;

pid = wait(NULL)      /* ignore status information */

pid = wait(&status);  /* status will contain status information */
```
일반적으로 상태(status) 정보는 exit를 통해 전달된 자식의 exit-status가 된다.

차일드를 만들고, 차일드는 특정 작업을 하고  
패런트는 차일드를 기다리다가, 차일드가 스테이터스 값을 넘겨주고  
그걸 패런트가 받아서 어떤 차일드인지 출력한다.  
혹은 NULL로 해서 스테이터스를 안 받을 수도 있다.

### example (2/2)
```c
main(){	 /* status -- 자식의 퇴장(exit) 상태를 어떻게 알아내는지 보여준다 */
   pid_t pid;
   int status, exit_status;
   
   if((pid=fork()) < 0) fatal("fork failed");
   
   if(pid==0){	/*자식*/
      /* 이제 수행을 4초동안 중단시키기 위해 라이브러리 루틴 sleep을 호출 한다 */
      sleep(4);
      exit(5); /* 0이 아닌값을 가지고 퇴장 */
   }
   
   /* 여기까지 수행이 진전된 바 이것은 부모임. 자식을 기다린다. */
   if((pid=wait(&status)) == -1){
      perror("wait failed");
      exit(2);
   }

   /* 자식이 어떻게 죽었는지 알기 위해 테스트한다 */
   if (WIFEXITED(status)){
      exit_status = WEXITSTATUS(status);
      printf("Exit status from %d was %d\n", pid,exit_status);
   }
   exit(0);
}
```
포크해서 차일드가 만들어 지고,  
pid가 0인 차일드의 경우, 4초 쉬고 엑시트로 5를 준다.

부모는 4초간 웨이트하다가 깨어나서 pid에 차일드의 pid가 리턴되고,  
5는 스테이터스에 리턴되는데,  
스테이터스에는 5뿐만 아니라 또 다른 값도 있다(정상종료인지 알아보는 값).

`WIFEXITED`는 5만 뽑아내는 매크로, 상태만 뽑아내는 매크로가 존재한다.  
매크로니까 W로 시작하고, IF 만약, EXITED exit으로 종료 되었느냐(정상 종료 되었느냐)?

정상 종료되면 결과가 트루가 된다(비정상 종료되면 펄스).  
정상 종료되면 엑시트 스테이터스를 뽑아내는 매크로  
W(매크로) EXIT STATUS로 값만 뽑아내 변수에 담는다.  
이제 그 값을 출력할 수 있다.

## The waitpid(2) system call
### The waitpid(2) system call(1/2)
```c
#include <sys/wait.h>

pid_t waitpid(pid_t pid, int *statloc, int options); 

//Return: child process ID if OK, 0 (see later), or -1 on error
```
- 앞서 언급했듯이, 자식이 둘 이상인 경우 자식 중 하나라도 종료되면 wait이 반환(return)됩니다.
- 인자(Argument)
  - pid
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG059.jpg "그림04"){: .align-center}

차일드가 여러 개 있을 때,  
어떤 차일드가 죽어서 반환되었는지는 리턴 값으로 알 수 있다.  
차일드를 지정해 그 차일드가 죽으면 깨어나게 해준다.

옵션이 추가로 있다.  
- pid가 -1이면 그냥 wait랑 같다.
  - 차일드를 지정 안 했기 때문에.
- pid가 0보다 크면 해당 pid를 가진 특정 차일드를 기다린다.
- pid가 0이면 패런트가 속한 프로세스 그룹 아이디와
  - 프로세스 들의 그룹이 있고, 그 그룹 아이디를 지정 가능
    - 같은 그룹 프로세스 들을 아무거나 기다린다.
- pid가 0보다 작으면(-1이 아닌) 절댓값으로 바꿔서,
  - 해당 pgid(프로세스 그룹 아이디)에 있는 차일드 중 아무거나 종료될 때까지 기다린다

### The waitpid(2) system call(2/2)
- option
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG060.jpg "그림05"){: .align-center}
- 이 인자는 0이거나 상수의 비트별(bitwise) OR로 구성됩니다.
```c
 waitpid(chidpid, &status, WCONTINUED)
 waitpid(chidpid, &status, WUNTRACED) 
```

W는 웨이트(wait), NO는 아니, HANG (wait,hang,block은 비슷한 의미, 목을 매단다.)  
즉 기다리지 말라는 옵션이다.  
`waitpid`의 **WNOHANG** 옵션은 기다리지 않는다, 즉, 곧바로 리턴한다.

만약에 pid로 지정된 차일드가 즉시  
어베일어블(엑시트해서 스테이터스 전달)하지 않는다면,  
차일드가 계속 러닝하고 있어서 리턴했는지,  
차일드가 종료해서 리턴했는지는 리턴 값으로 구분한다.  
(러닝 중일 때 리턴하면 리턴 값은 0,  
엑시트해서 리턴하면 리턴 값은 차일드의 pid)  
차일드가 일하는 동안 부모는 아무 것도 못하니  
차일드의 상태를 확인하면서 부모도 동시에 일한다.

wait()은 좀더 정확히 말해서  
자식프로세스의 상태 변화를 확인하기 위한 시스템 콜이다.  
process job-scheduling이 안되던 예전 시스템들은  
방금 말한 내용이 적용되어 지지 않는다.  
즉, 이전에 설명한 것처럼 wait()는 자식프로세스의 종료 상태가 목적이다.  
최근 시스템들은 대부분 process job-scheduling이 지원됨으로 wait()는  
자식프로세스의 상태 변화를 확인하기 위한 시스템 콜로 봐도 무방할 것이다.

### example
```c
main(){ /* status2--waitpid를 사용하여 자식의 퇴장 상태를 어떻게 얻는지 본다 */
   pid_t pid;
   int status, exit_status;
   
   if((pid=fork()) < 0) fatal("fork failed");
   
   if(pid == 0){  /* 자식 */
      /* 이제 4초동안 중단시키기 위해 라이브러리 루틴 sleep을 호출한다 */
      sleep(4);
      exit(5);  /* 0이 아닌 값을 가지고 퇴장한다 */
   }
   /* 여기까지 수행이 진전된 바 이것은 부모임. 따라서 자식이 퇴장했는지 확인한다.
      퇴장하지 않았으면, 1초 동안 수면한 후 다시 검사한다. */
   while(waitpid(pid, &status, WNOHANG) == 0){  
      printf("Still waiting... \n");
      sleep(1);
   }

   /* 자식이 어떻게 죽었는지 알기 위해 테스트한다 */
   if (WIFEXITED(status)){
      exit_status = WEXITSTATUS(status);
      printf("Exit status from %d was %d\n", pid,exit_status);
   }
   exit(0);
}
```
포크해서 차일드를 만들고  
(부모면 자식pid, 자식이면 0 리턴)  
자식이면 4초동안 자다가 엑시트한다.

부모는 waitpid로 만든 차일드를 기다리는데,  
옵션으로 기다리지 않게 하고,  
리턴 값이 0이면(자식이 러닝 중이면) 스틸 웨이팅... 출력하고  
1초간 자고 다시 웨이팅 반복,  
리턴 값이 0이 아닐 동안 반복(차일드가 끝날 때 까지)한다.

차일드가 정상적으로 죽었는지 체크하는 과정이 필요하다.  
매크로로 스테이터스 값을 받아서 확인하고, 엑시트 값만 뽑아서 출력한다.

## wait(2) & waitpid(2) Functions
**두 함수의 차이점**

`wait`는 **무조건** 기다린다.  
child가 죽을 때 까지 caller를 차단하고 기다린다.(blocking)

`waitpid`는 **WNOHANG**으로 블록(차단)을 하지 않게 방지해줄 수 있다.  
waitpid 리턴 값으로 차일드 상태 확인 가능하다.  
어떤 프로세스를 기다릴지 제어하는 여러 가지 옵션이 있고,  
먼저 종료되는 차일드를 기다리지 않아도 된다.

## Confirm the exit status
### Confirm the exit status (1/2)
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG061.jpg "그림06"){: .align-center}
위 매크로들은 자식의 상태 변화를 확인하기 위한 매크로들이다.  
WIF로 시작하는 매크로들은 어떠한 형태로  
자식프로세스의 상태가 변경되었는지를 확인할 때 쓰인다.  
각 WIF카테고리 아래에 있는 매크로들은  
각 상태에 따른 정보들을 확인할 때 사용된다.

- WIFEXITED
  - 자식이 정상적으로 종료된 경우 0이 아닌 값을 준다.
- WEXITSTATUS
  - 정상 종료한 경우 확인하고 exit 값을 받는 매크로
- WIFSIGNALED
  - 시그널을 받고 종료되었는지 알아보는 매크로
- WTERMSIG
  - 어떤 시그널을 받고 종료했는지 시그널 번호를 알아보는 매크로
- WCOREDUMP
  - 프로세스가 디버깅 목적으로 메모리에 덤프하는 경우가 있는데, 덤프가 되었는지 알아보는 매크로
- WIFSTOPPED
  - 프로세스가 스톱되는 경우가 있고
  - 프로세스가 스톱되었다가 다시 리줌, 컨티뉴 되는 경우
  - 프로세스가 실행중에 스톱 시그널을 주면 일시중지 된다
  - 잠시 중단되었는지를 알 수 있다.
- WSTOPSIG
  - 스톱 시그널이 2개 있는데, 어떤 스톱 시그널인지 알아보는 매크로
- WIFCONTINUED
  - 컨티뉴 시그널로 다시 재귀하면 재귀했다는 상태를 알 수 있는 매크로

### Confirm the exit status (2/2)
```c
#include <sys/wait.h>

void pr_exit(int status)
{
   if (WIFEXITED(status))
      printf("normal termination, exit status = %d\n",
         WEXITSTATUS(status));
   else if (WIFSIGNALED(status))
      printf("abnormal termination, signal number = %d%s\n",
         WTERMSIG(status),
#ifdef  WCOREDUMP
         WCOREDUMP(status) ? " (core file generated)" : "");
#else
         "");
#endif
   else if (WIFSTOPPED(status))
      printf("child stopped, signal number = %d\n",
         WSTOPSIG(status));
}
```
WIFEXITED 정상 종료했냐?  
맞다면 WEXITSTATUS로 스테이터스 값을 가져온다.

WIFSIGNALED 시그널을 받고 종료했냐?  
그렇다면 WTERMSIG로 어떤 시그널을 받았는지 시그널 번호를 가져온다.

WCOREDUMP 코어덤프 했냐?  
트루면 코어 덤프가 되었다는 것

WIFSTOPPED 스탑 시그널 받았냐?  
그렇다면 WSTOPSIG 어떤 스탑 시그널을 받았냐?

# 5.8 Zombie and premature exits
## Zombie Process (defunct process)
### Zombie Process (defunct process) (1/2)
- 부모와 자식 프로세스가 싱크로나이즈드하다가 잘못 되었을 때
  - 좀비 프로세스(Premature exits)가 만들어질 수 있습니다.
- 부모가 종료된 자식을 wait()하지 않으면 좀비가 됩니다.
  - ps에서 볼 수 있는 상태 **"Z"**
- 좀비는 부모가 wait() 또는 waitpid()를 호출할 때까지 맴돌아다닙니다.
- 프로세스가 실행을 완료(exit해서 죽음)했지만
  - 프로세스 테이블에 여전히 항목이 있는 프로세스입니다.

```c
while(pid=fork()){
   if(++count==3) break;
}
    
if (pid == 0){
   sleep(5);
   printf("I will be back %d\n", getpid());
   exit(0);	/* 자식은 먼저 죽는다 */
}
else if(pid > 0){
   printf("Im parent %d\n", getpid());
   printf("Press any key\n");
   getchar();	/* 자식이 죽을 때까지 기다린다 */
}
```
자식이 엑시트해서 스테이터스 값을 부모 웨이트에 전달하면 자식이 죽어야 한다.  
그런데 자식이 죽었는데 부모가 웨이트를 안하고 있으면 자식은 죽지 않는다.  
(프로세스 엔트리에서 값이 클리어 되어야 프로세스 테이블에서 안 나타나는데,  
스테이터스가 전달이 안 되면 사라지지 않는다.)

좀비는 부모가 wait(pid)를 할 때까지 기다린다.  
실행을 종료했는데도 불구하고,  
프로세스 테이블에 엔트리가 그래도 남아있는 경우다.

포크해서 차일드를 만들고 와일 루프를 돈다.  
차일드면 루프를 곧바로 나간다.  
(부모만 루프를 돈다, 차일드는 포크해서 pid가 0이라 바로 빠져나간다.)

차일드는 5초간 잠들어있다가 아 윌 비 백하고서 죽는다.  
부모는 루프 3번 돌고 나가서 자기 pid를 나는 부모다 하면서 출력하고  
아무 키나 누르라고 하고 getchar를 실행한다.  
(키보드로 입력이 들어올 때까지 기다린다.)

부모가 웨이트를 안 해줘서 차일드 3개 모두 좀비가 된다.

### Zombie Process (defunct process) (2/2)
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG062.jpg "그림07"){: .align-center}
차일드가 엑시트해서 죽으면 **SIGCHLD**라는 시그널을 패런트한테 보내준다.  
웨이트를 실행중이면 시그널을 받고 깨어나는데,  
웨이트를 안 해주면 그 기간동안 차일드는 좀비가 된다.

## Orphan  Process
### Orphan  Process (1/2)
- 부모 프로세스가 죽으면 자식 프로세스는 고아 프로세스가 됩니다.
- **init** 프로세스(pid = 1)는 고아 프로세스의 부모가 됩니다.
- **init** 프로세스는 주기적으로 자식을 기다리므로 결국 고아 좀비는 제거됩니다.

```c
while(pid=fork()){
   if(++count==3) break;
}
    
if (pid == 0){
   printf("I will be back %d\n", getpid());
   sleep(500);
   exit(0);
}
else if(pid > 0){
   printf("Im parent %d\n", getpid());
   printf("Press any key\n");
   getchar();	/* 먼저 엔터를 치고 부모는 종료한다 */
}
```
차일드가 살아있는데 패런트가 죽으면,  
차일드는 올판(Orphan, 고아) 프로세스가 된다.  
init 프로세스가 주기적으로 고아 프로세스를 찾아 입양을 한다.  
init 프로세스가 주기적으로 자식 프로세스에 웨이트를 실행해준다.  
init 프로세스가 좀비 프로세스를 R.I.P 해준다.

부모는 포크 3번 실행,  
자식은 500초간 잠들어 있다 종료한다.

부모는 나는 부모다하고 엔터키를 바로 쳐버린다.  
그러면 부모가 먼저 죽어버린다.  
그러면 자식은 올펀(고아)이 되는데,  
init이 주기적으로 입양한다(adoptation).

그러면 init이 주기적으로 wait 해준다.  
그동안(init이 wait하기 전까지) 차일드는 좀비 상태다.

### Orphan  Process (2/2)
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG063.jpg "그림08"){: .align-center}
위에서 설명한 내용에 관한 이미지입니다.

## 마무리
이상으로 Linux 이론의 프로세스2편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
