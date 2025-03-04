---
title:  "Linux 기초 이론 08 PROCESS & SHELL"
excerpt: "Linux 기초 이론 여덟 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-03-04T24:00:00-00:00
---

# Linux Lecture Theory 08 PROCESS & SHELL
## Introduction
지난 장에 이어 프로세스와 셸의 대해 설명하겠습니다.

# 5.9 smallsh: a command processor
## Basic logic of command processor
```
while(EOF not typed)
{

	Get command line from user  <- userin()

	Assemble command args and execute <- procline()

        Wait for child

}
```
셸의 처리과정은 다음과 같다.  
1. 사용자 입력을 받는다.
2. 명령을 실을 프로세스를 생성한다.(fork())
3. 새로 생성된 자식 프로세스는 입력 받은 문자열을 exec호출에 적합한 형태로 구성한다.
4. exec호출을 한다.

이 4단계를 계속적으로 반복 하는 것이 셸이다.

## The steps in executing the ls
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG064.jpg "그림01"){: .align-center}
**fork**  
- 새로 생성된 자식프로세스를 위한 proc 테이블 엔트리를 할당.
- 부모프로세스의 proc 테이블 엔트리를 자식프로세스 proc 테이블 엔트리에 복사
- 자식 프로세스를 위한 스택과 사용자 공간을 할당.
- 자식 프로세스에게 새로운 pid번호를 할당
- 부모프로세스의 text 영역이 공유되도록 설정.
- 데이터와 스택 영역을 복사
- open된 파일들이 공유될 수 있도록 설정.
- 부모 프로세스의 레지스터 값들을 자식에게 복사.

**exec**  
- exec()에 주어진 실행파일을 검색
- 현재 프로세스가 해당 실행파일을 실행할 수 있는지 퍼미션 테스트.
- 실행파일이 실행모듈인지 셸-스크립트인지를 구분하기 위해 파일의 header로부터 magic number를 읽음.
- exec()에 주어진 arguments 와 environ 파라미터를 커널로 복사.
- 새로운 프로그램 레이아웃으로 변경하기 위해 이전 메모리 레이아웃을 할당 해제.
- 새로운 메모리 레이아웃 공간을 할당.
- arguments들과 environ을 커널로부터 얻어서 main함수로 넘김.
- 현재 프로세스의 signal들과 레지스터들을 초기화

![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG065.jpg "그림02"){: .align-center}

## smallsh - Header & global variables 
```c
/* smallsh.h –smallsh 명령 처리기를 위한 정의들 */
#include <unistd.h>
#include <stdio.h>
#include <sys/wait.h>

#define EOL		1
#define ARG		2
#define AMPERSAND	3
#define SEMICOLON	4

#define MAXARG 512
#define MAXBUF 512

#define FOREGROUND 0
#define BACKGROUND 1
```
```c
/* 프로그램 버퍼 및 작업용 포인터들 */
static char inbuf[MAXBUF], tokbuf[2*MAXBUF], 
            *ptr = inbuf, *tok=tokbuf;
```
FOREGROUND : 셸이 명령실행 시 처리 될 때까지 기다린다.  
BACKGROUND : 셸은 명령을 실행하고 곧바로 다른 명령을 처리할 수 있도록 대기한다.

## smallsh - userin()
```c
int userin(char *p) {
   int c, count;
   ptr = inbuf;	tok = tokbuf;
   printf("%s", p);	/* 명령 프롬프트 출력 */
   /* p --> char *prompt = "Command> "; */
	
   count = 0;	/* 사용자 입력 문자개수를 저장하기 위한 카운트 변수 */
   while(1){
      if( (c=getchar()) == EOF)	/* EOF == ^D */
         return EOF;
			
      if( count < MAXBUF )
         inbuf[count++] = c;
		
         if( (c=='\n') && (count < MAXBUF) ){
            inbuf[count] = '\0';
            return count;
         }
		
         if( c=='\n' ){
            printf("smallsh: inputline too long\n");
            count = 0;
            printf("%s",p);
         }
   }
}
```
userin()은 새로운 명령 입력에 따라 procline()처리 시,  
inpbuf와 tokbuf을 조작하기 위한 ptr과 tok를 초기화한다.
### MAXBUF
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG066.jpg "그림03"){: .align-center}

## smallsh - procline()
```c
int procline(){
   char* arg[MAXARG+1]; /* runcommand 를 위한 포인터 배열 */
   int toktype = 0; /* 명령 내 토큰의 유형 */
   int narg = 0;	/* 지금까지 인자 수 */
   int type; /* foreground 또는 background */
   
   for(;;) { // infinite loop
      switch(toktype = gettok(&arg[narg])) {
         case ARG: if(narg < MAXARG) narg++; break;
         case EOL:
         case SEMICOLON:
         case AMPERSAND:
            if ( toktype == AMPERSAND ) type = BACKGROUND;
            else type = FOREGROUND;
            
            if ( narg != 0 ){
               arg[narg] = NULL;
               runcommand(arg, type);
            }
            if( toktype == EOL ) return;
            narg = 0;
            break;
      }        
   }
}
```
[MAXBUF](https://ji-hun-park.github.io/linux/Linux-Theory-08/#maxbuf)는 위와 동일하다.  
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG067.jpg "그림04"){: .align-center}

## smallsh - gettok()
```c
/* 토큰을 가져와서 tokbuf에 넣는다. */
int gettok (char **outptr){
   int type;

   *outptr = tok; /* outptr 문자열을 tok로 지정한다. */

   while ( *ptr == ' ' || *ptr == '\t' ) /* 토큰을 포함하고 있는 버퍼로부터 여백 제거 */
      ptr++;  // 1. 하단 그림의 ptr

   *tok++ = *ptr; /* 토큰 포인터를 버퍼 내의 첫 토큰으로 지정한다. */
   // 2. 하단 그림의 tok

   switch (*ptr++){ /* 버퍼 내의 토큰에 따라 유형 변수를 지정한다. */
   case '\n':  type = EOL;        break;
   case '&':   type = AMPERSAND;  break;
   case ';':   type = SEMICOLON;  break;
   default:    type = ARG;
      /* 유효한 보통 문자들을 계속 읽는다. */
      while (inarg (*ptr))
         *tok++ = *ptr++;
   }
   /* special [] = {' ', '\t', '&', ';', '\n', '\0'}; */
   *tok++ = '\0'; // 3. 하단 그림의 마지막 블록(버퍼)
   return type;
}
```
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG068.jpg "그림05"){: .align-center}

## smallsh - runcommand()
```c
int runcommand(char **cline, int where){ /* wait를 선택사항으로 명령을 수행 */
   pid_t pid;
   int status;
  
   switch (pid = fork()) {
   case -1:
      perror ("smallsh");
      return (-1);
   case 0:
      execvp (*cline, cline);
      perror (*cline);
      exit (1);
   }
   /* 부모의 코드 - 만일 백그라운드 프로세스이면 프로세스 식별자를 프린트하고 퇴장한다 */
   if (where == BACKGROUND){
      printf ("[Process id %d]\n", pid);
      return (0);
   }

   /* 프로세스 pid가 퇴장할 때까지 기다린다. */
   if (waitpid (pid, &status, 0) == -1)
      return (-1);
   else
      return (status);
}
```

## smallsh - inarg(), main()
```c
static char special [] = {' ', '\t', '&', ';', '\n', '\0'};

int inarg (char c)
{
   char *wrk;

   for (wrk = special; *wrk; wrk++){
      if (c == *wrk)
         return (0);
   }

   return (1);
}
```
```c
#include "smallsh.h"

char *prompt = "Command> ";	/* 프롬프트 */

main()
{
   while(userin(prompt) != EOF)
      procline();
}
```

# 5.10 Process Attributes
## The process-id
```c
#include <unistd.h>

pid_t getpid(void);
          // Returns: process ID of calling process

pid_t getppid(void);
          // Returns: parent process ID of calling process

uid_t getuid(void);
          // Returns: real user ID of calling process

uid_t geteuid(void);
          // Returns: effective user ID of calling process

gid_t getgid(void);
          // Returns: real group ID of calling process

gid_t getegid(void);
          // Returns: effective group ID of calling process
```
프로세스에서 가장 중요한 속성인 프로세스 아이디에도 여러 가지 종류가 있는데,  
프로세스 아이디, 패런트 프로세스 아이디,  
리얼 유저 아이디, 이펙티브 유저 아이디,  
리얼 그룹 아이디, 이펙티브 그룹 아이디(리얼에는 r이 없다)가 있다.

프로세스 아이디가 `0`인 것과 `1`인 것이 있는데, 이 들은 **OS 프로세스**다.  
`1`번이 가장 중요한 **Init** 프로세스(모든 프로세스의 조상)다.

## Process ID 0 & 1
- 스케줄러 프로세스(swapper, 스왑퍼): 프로세스 ID 0
  - **디스크에 있는 그 어떤 프로그램도 이 프로세스에 해당하지 않습니다.**
  - 이 프로세스는 커널의 일부이며 시스템 프로세스로 알려져 있습니다.

- Init 프로세스: 프로세스 ID 1
  - 부트스트랩 절차(bootstrap procedure)가 끝날 때 커널에서 호출합니다.
  - 이 프로세스의 프로그램 파일은 이전 버전의 UNIX 시스템에서는 `/etc/init`이고 최신 버전에서는 `/sbin/init`입니다.
  - 이 프로세스는 UNIX 시스템을 시작하는 역할을 합니다.
  - init 프로세스는 절대 죽지 않습니다.
  - 스왑퍼와 같이 커널 내의 시스템 프로세스가 아닌 일반 사용자 프로세스이며 슈퍼유저 권한으로 실행됩니다.

process id 0는 스케쥴러 프로세스이며, 종종 swapper로서 알려져 있다.  
이 프로세스에 대응되는 프로그램은 디스크 상에 존재하지 않는다.  
시스템 프로세스로 커널의 일부분이다. 유닉스 시스템을 구동한다.

## example
```c
static int num = 0;
static char namebuf[20];
static char prefix[] = "/tmp/tmp";

char *gentemp(void){
   int length;
   pid_t pid;

   pid = getpid();	/* 프로세스 식별번호를 얻는다. */

   /* 표준 문자열처리 루틴들  <string.h> */
   strcpy (namebuf, prefix);
   length = strlen(namebuf);

   /* 파일 이름에 프로세스 식별번호(pid)를 추가한다. */
   itoa (pid, &namebuf[length]);	/* filename : /tmp/tmp#pid */

   strcat (namebuf, ".");
   length = strlen (namebuf);
   do{
   	itoa (num++, &namebuf[length]); 	/* 접미 번호를 추가한다. */
   } while (access(namebuf, F_OK) != -1); /* test for existence of file */
   
   return (namebuf); 		/* filename : /tmp/tmp#pid.#num */
}

/* itoa --정수를 문자열로 변환한다. */
int itoa(int i, char *string)
{
   int power, j;
  
   j = i;
  
   for (power = 1; j >= 10; j /= 10) /* i = 1234, power=1000 */
      power *= 10;
  
   for ( ; power > 0; power /= 10)
   {
      *string++ = '0' + i/power;
      i %= power;
   }
  
   *string = '\0'; 
}
```
>질문: atoi는 어떻게 구현할까요?

## Process groups and process group-ids
### Process groups and process group-ids (1/2)
하나 이상의 프로세스 들을 그룹으로 묶을 수 있다.  
그 그룹에 아이디를 붙이면 프로세스 그룹 아이디가 된다.  
앞에서 말한 리얼, 이펙티브는 유저 관련이고,  
여기선 순수하게 프로세스 들에게 붙여진 숫자다.

프로세스 그룹은 주로 연관된 동일한 작업을 하는,  
프로세스들의 그룹(파이프를 주고 받는 프로세스)이다.

각각의 프로세스 그룹은 유니크한 그룹 아이디를 갖는다.  
이는 프로세스 아이디와 유사하다(유저 그룹 아이디와는 다르다).  
(양의 정수, pid_t 타입을 가진다.)

그룹 안에 여러 프로세스들 중 하나 만이 그룹 리더가 된다.  
그룹 리더 아이디가 그룹 아이디와 같다.  
(프로세스 그룹 리더의 pid == pgid)  
```c
#include <unistd.h>

pid_t getpgrp(void);
		// Returns: process group ID of calling process

pid_t getpgid(pid_t pid);	/* getpgid(0) == getpgrp() */
		// Returns: process group ID if OK, -1 on error
```
`unistd` 헤더를 인클루드하고,  
`rp`가 붙은 건 해당 시스템 콜을 부르는 프로세스의 프로세스 그룹 아이디.  
즉, 자기 자신의 프로세스 그룹 아이디다.

`id`가 붙은 건 여기서 인자로 넘겨준 프로세스의 그룹 아이디를 알 수 있다.  
(0을 넘겨주면 rp랑 같다.)

보통 프로세스의 `pgid`는 부모로부터 상속 받는다.

### Process groups and process group-ids (2/2)
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG069.jpg "그림06"){: .align-center}
자기(프로세스) 아이디, 부모 아이디, 그룹 아이디, 세션 아이디, TPGID, 명령어  
(TPGID 필드는 세션 내의 포그라운드 프로세스 그룹을 나타낸다. 이 경우 ps의 PGRP다.  
ps는 세션에서 현재 실행 중인 명령이기 때문이다.)

`ps` 명령은 이런 title을 출력하고, 프로세스 리스트를 본다.  
`cat`에 입력이 없으면, 키보드로 입력을 받아서 스크린으로 출력한다.  
여기선 앞에 문장이 출력된다.

#셸이 bash, ps와 cat은 패런트가 bash,  
프로세스 그룹 아이디랑 같은 pid인 ps가 프로세스 그룹의 리더다.

## Changing process group
```c
#include <unistd.h>

int setpgid(pid_t pid, pid_t pgid);
 
    // Returns: 0 if OK, -1 on error	
```
이 함수는 프로세스 ID가 `pid`와 같은 프로세스에서  
프로세스 그룹 ID를 `pgid`로 설정합니다.  
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG070.jpg "그림07"){: .align-center}
프로세스는 **자기 자신 또는 자식 중 하나의** 프로세스 그룹 ID만 설정할 수 있습니다.  
게다가 자식이 exec 함수 중 하나를 호출한 후에는,  
자식 중 하나의 프로세스 그룹 ID를 변경할 수 없습니다.

프로세스 그룹을 바꿔주는 함수.  
포크해서 자식을 만들면 부모와 같은 프로세스 그룹 아이디를 상속받는다.

부모가 자식을, 혹은 자식 스스로  
프로세스 그룹 아이디를 바꿀 수 있는데 그걸 해 주는게 `setpgid`다.  
앞의 인자가 프로세스 아이디,  
뒤의 인자는 바꿀 프로세스 그룹 아이디다.

여기서 pid와 pgid를 같은 값을 주면,  
해당 프로세스가 프로세스 그룹의 리더가 된다.

pid를 0으로 주면 자기 자신을 뜻한다.  
pgid를 0으로 주면 자기 자신의 pid를 pgid로 주는데,  
새로운 그룹을 생성하고 그 그룹의 리더가 된다.

프로세스는 자기 자신의 그룹 아이디랑  
자신의 자식의 그룹 아이디만 바꿀 수 있다.  
다만 차일드가 exec로 다른 기능으로 변신을 한 이후에는  
부모가 자식 그룹을 바꿀 수 없다.

## Sessions and session-ids
### Sessions and session-ids (1/3)
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG071.jpg "그림08"){: .align-center}
프로세스 그룹에는 여러 개의 프로세스들이 들어있는데,  
세션에는 프로세스 그룹이 여러 개 있다.(하나 이상의 프로세스 그룹 모음)  
한 프로세스 그룹의 프로세스들은 pipe로 연결되어 있다.

터미널을 실행해 로그인하면 가장 처음 실행되는 프로세스가 셸인데,  
셸이 명령어를 실행할 때마다 자식을 계속 만들고,  
그 자식들이 자식을 만들어 계층 구조가 만들어진다.  
그러나 셸은 컨트롤링 터미널(controlling terminal) 안에 고정되어 있다.

셸에서 명령어를 입력하고 &를 입력하면 백그라운드에서 실행된다.  
자식이 실행할동안 부모가 기다리지 않는데,  
이런 프로세스를 백그라운드 프로세스라고 한다.

백그라운드로 실행하면 백그라운드 그룹으로 들어가고,  
포그라운드에서 실행하면 포그라운드 그룹에 들어간다.  
`백그라운드 그룹`은 **여러 개**가 있을 수 있지만,  
`포그라운드 그룹`은 **한 개** 밖에 없다.

### Sessions and session-ids (2/3)


### Sessions and session-ids (3/3)


## 작성중
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG072.jpg "그림09"){: .align-center}

## 마무리
이상으로 Linux 이론의 프로세스 & 셸편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
