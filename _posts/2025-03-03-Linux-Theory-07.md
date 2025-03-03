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

## 작성중
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG058.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG059.jpg "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG060.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG061.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG062.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG063.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG064.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG065.jpg "그림10"){: .align-center}
#### MAXBUF
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG066.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG067.jpg "그림12"){: .align-center}
[MAXBUF](https://ji-hun-park.github.io/linux/Linux-Theory-07/#maxbuf)
![그림13](https://ji-hun-park.github.io/assets/images/LNXIMG068.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/LNXIMG069.jpg "그림14"){: .align-center}
![그림15](https://ji-hun-park.github.io/assets/images/LNXIMG070.jpg "그림15"){: .align-center}
![그림16](https://ji-hun-park.github.io/assets/images/LNXIMG071.jpg "그림16"){: .align-center}
![그림17](https://ji-hun-park.github.io/assets/images/LNXIMG072.jpg "그림17"){: .align-center}

## 마무리
이상으로 Linux 이론의 프로세스2편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
