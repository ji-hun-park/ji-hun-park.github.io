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

   while ( *ptr == ' ' || *ptr == '\t' ) /*토큰을 포함하고 있는 버퍼로부터 여백 제거*/
      ptr++;

   *tok++ = *ptr; /* 토큰 포인터를 버퍼내의 첫 토큰으로 지정한다. */

   switch (*ptr++){/*버퍼내의 토큰에 따라 유형 변수를 지정한다. */
   case '\n':  type = EOL;        break;
   case '&':   type = AMPERSAND;  break;
   case ';':   type = SEMICOLON;  break;
   default:    type = ARG;
      /* 유효한 보통문자들을 계속 읽는다. */
      while (inarg (*ptr))
         *tok++ = *ptr++;
   }

   *tok++ = '\0';
   return type;
}
```
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG068.jpg "그림05"){: .align-center}

## 작성중
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG069.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG070.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG071.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG072.jpg "그림09"){: .align-center}

## 마무리
이상으로 Linux 이론의 프로세스 & 셸편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
