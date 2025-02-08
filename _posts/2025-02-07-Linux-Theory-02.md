---
title:  "Linux 기초 이론 02 FILE1"
excerpt: "Linux 기초 이론 두번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-02-07T20:00:00-23:59
---

# Linux Lecture Theory 02 FILE1

## Introduction
이번 장에서는 프로그램 내에서 파일을 처리하기 위해 UNIX가 제공하는 기본적인 사항을 살펴보겠습니다.  
기존 쓰기 힘든 `메인 프레임` `OS`에서 쓰기 편한 `유닉스`로 바뀐 후에 파일 시스템에 관해 설명하겠습니다.

## File and Filesystem
![그림00](https://ji-hun-park.github.io/assets/images/그림23.jpg "그림00"){: .align-center}
### File
* 데이터를 포함하는 컨테이너
* 파일은 일렬의 연속된 바이트 시퀀스입니다.
* 운영 체제에서 부과하는(정한) 어떠한 형식(포맷)도 없습니다.
    + 이전에 IBM은 ISAM, RF, VSAM 등 여러 포맷이 있어 엑세스하는 API가 다 달랐습니다.(리드, 라이트 방법)
        - OS가 다 정해줬었습니다.
        - OS차원에서만 걷어냈을 뿐 해당 포맷들의 기능들은 사용 가능합니다.
* 각 바이트는 디스크 파일에서 개별적으로 주소 지정 가능합니다.(Addressable)
* 파일은 또한 외부 장치를 포함해 균일한(Uniform) 인터페이스입니다.(read, write)

### Filesystem
* 컴퓨터 파일과 데이터를 저장하고 구성(Organizing)하는 방법.
    - 기존 미니 디스크는 하이라키컬한(계층적) 구조가 없었습니다.
* 파일을 쉽게 찾고 액세스할 수 있도록 합니다.
* 파일 시스템은 데이터 저장 장치인 하드 디스크, CD-ROM, USB, 마그네틱 테이프 등을 사용할 수 있습니다.

# 2.1 UNIX file access primitives
## UNIX primitives
![그림01](https://ji-hun-park.github.io/assets/images/그림24.jpg "그림01"){: .align-center}

> 오픈 – 파일을 오픈, 리드나 라이트를 하거나 혹은 생산하거나  
크리에이트 – 없던 파일을 새로 만듦  
클로즈 – 오픈한 파일을 닫을 때  
리드 – 오픈한 파일의 데이터를 읽을 때  
라이트 – 오픈한 파일에 데이터를 쓸 때  
엘시크 – 디스크(랜덤 엑세스 가능한)에서만 쓰고, 파일은 바이트 단위로 주소를 줄 수 있는데 바이트 단위로 위치를 추적해서 위치 이동할 때  
언링크, 리무브 – 파일을 지움  
파일 컨트롤 – 파일에 관한 어트리뷰트들을 변경시키거나 읽을 때

```
엘시크 빼고는 사용법이 전부 똑같습니다.(단순함, 쓰기 쉬움)
```

+ 이 함수는 ANSI C의 일부가 아니지만 POSIX.1 및 XPG3의 일부입니다.
    - ANSI C(American National Standard Institute C, 미국 공립 표준국 C)
        - C에 관한 미국 표준(국제 표준이 아님)
    - XPG3는 X/Open POSIX.1은 IEEE에서 정한 국제 표준
+ UNIX primitives 9개는 미국 표준이 아닌 국제 표준입니다.
+ 이 표준들과 이 장에서 설명하는 함수는 I/O에 관련된 `버퍼링되지 않은 I/O(unbuffered I/O)`라고 합니다.
    - 라이브러리 안에 버퍼가 없음
    - 커널에 곧바로 접속
    - read를 하면 커널에 바로 접속하지만, fread는 라이브러리를 거치는데, 라이브러리에 버퍼가 없음
    - 커널에 버퍼가 있어도, 유저 메모리에는 버퍼가 없음
+ `버퍼링되지 않은 I/O`는 open, read, write, lseek 및 close 함수에서 제공합니다. 이러한 함수는 모두 파일 설명자와 함께 작동합니다.

파일 시스템 콜의 인터페이스는 기본적으로 위 이미지에 나와있는 9개 입니다.  
유저 프로그램이 시스템 콜을 사용해서 파일 시스템을 엑세스합니다.  
9개의 시스템 콜을 연결할 때마다 트랩(trap, 소프트웨어 인터럽트)가 발생합니다.  
유저 앱이 시스템 콜을 이용해 파일 시스템 메니지먼트를 통해 캐릭터냐 블록이냐에 따라 디바이스 드라이버를 거쳐 하드웨어 컨트롤에 의해 하드웨어(블락(디스크), 캐릭터(키보드, 스크린, 프린터)) 를 제어합니다.(캐릭터는 1바이트 단위라 상관없고, 블락(4K바이트,4896바이트) 단위일 경우 버퍼 캐시를 거침)

## File Descriptor
### File Descriptor
+ 커널에서 모든 열린 파일은 `파일 서술자(파일 설명자, File Descriptor)`에 의해 참조됩니다.
+ 음수가 아닌 정수(사용되지 않는 가장 낮은 정수).
+ 기존 파일을 열거나 새 파일을 만들 때 커널은 프로세스에 `파일 서술자`를 반환합니다.
+ 읽기 또는 쓰기 인수.
+ Unix 셸에서 만든 각 프로세스는 터미널과 연결된 세 개의 열린 파일(아래 이미지 참조)로 시작합니다.

![그림02](https://ji-hun-park.github.io/assets/images/그림25.jpg "그림02"){: .align-center}
파일 서술자는 파일을 오픈할 때 넘어오는 번호입니다.(음수가 아닌 번호)  
파일을 새로 만들거나 기존 파일을 오픈할 때 음수가 아닌 번호를 지정합니다.  
이 번호로 파일에 read나 write 가능합니다.

유닉스에서 `프로세스`를 만들면 기본적으로 `3개`의 `파일 디스크립터(파일 서술자)`가 만들어집니다.  
>0번 표준 입력(키보드)  
1번 표준 출력  
2번 표준 에러  
1번과 2번 둘 다 스크린(모니터)  
정상 출력이냐 에러 출력이냐 차이

### example
```c
/* a rudimentary example program */

/* these header files are discussed below */
#include <fcntl.h>
#include <unistd.h>

main()
{
    int fd;
    ssize_t nread;
    char buf[1024];

    /* open file “data” for reading */
    fd = open(“data”, O_RDONLY); 

    /* read in the data */
    nread = read(fd, buf, 1024);

    /* close the file */
    close(fd);
}
```
![그림03](https://ji-hun-park.github.io/assets/images/그림26.jpg "그림03"){: .align-center}
#### Primitive System Data Types
* _t로 끝나는 이러한 데이터 유형을 원시 시스템 데이터 유형이라고 합니다.
* 일반적으로 헤더 <sys/types.h>에 정의되어 있습니다.(헤더 <unistd.h>에 포함되어야 함)
* 목적은 프로그램이 특정 데이터 유형(int, float..)을 사용하지 못하도록 하여 각 구현이 특정 시스템에 필요한 데이터 유형을 선택할 수 있도록 하는 것입니다.

#### 설명
fd는 int 타입으로 선언합니다.  
open을 실행하면 정수형으로 파일 서술자 번호를 반환(return)합니다.  
fd = open(패스네임(경로명), 옵션)을 처음 실행하면 3번부터 나옵니다.(0~2는 기본으로 잡혀 있음, 사용하지 않는 번호중 가장 낮은 번호)  
여기서 fd의 값은 3이 됩니다.

ssize_t 타입 – `프리미티브 시스템 데이터 타입(Primitive System Data Types, 주로 _t로 끝남)`으로 unistd 헤더에서 지정해 놓은 타입, `버전`마다 `read return 타입이 다른`데 이걸 `범용성있게 사용하게 해 줌`, `표준화 문제 해결`(#typedef ssize_t int, sys/types.h에 저장되어 있음, `유닉스`에서 없음)

data – relative pathname(상대 경로명)

O_RDONLY – 오픈 리드 온리(읽기 전용으로 열기)

이 번호(파일 서술자, 여기선 fd)를 read에다 집어 넣고  buf는 사이즈, 마지막은 읽어들일 사이즈  
read를 실행하면 버퍼 사이즈 만큼 읽어서 buf에다 넣습니다.  
nread에는 읽은 바이트 수가 return됩니다.  
close(fd)해서 끝냅니다.

include에서 <fcntl.h>는 /usr/include/fcntl.h를 나타냅니다.  
/usr/include/가 디폴트 디렉토리 “ ”로 경로 지정 가능합니다.  
fcntl.h는 헤더 파일인데 open 시스템 콜을 쓸 때 필수로 include 해야합니다.

## The open(2) system call
### () 설명
>()안에 숫자에 따라  
1: 커멘드, 2: 시스템 콜, 3: 라이브러리  
를 의미합니다! (해당 표기는 뒤에도 계속 나올 예정)

### The open(2) system call (1/2)
```c
#include <fcntl.h> 

int open(const char *pathname, int flags, [mode_t mode]);


//Returns: file descriptor if OK, -1 on error 
```
* 파일은 open 함수 호출에 의해 열리거나 생성됩니다.

오픈 시스템 콜을 쓰기 위해 `fcntl.h` 헤더 파일을 반드시 인클루드해야 합니다.  
[]안에 있는 건(mode) 필수가 아닌 선택 사항(옵션).  
상수 캐릭터의 *(포인터)는 문자열(string) 파일 네임(패스네임).  
플래그는 아규먼트(RDONLY - 0, 읽기만 할 것인지, WRONLY - 1, 쓰기만 할 것인지, RDWR – 2, 둘 다 할 것인지).

> Arguments
>> - flags:
>>> + O_RDONLY	#0	Open for reading only.(읽기 전용)
>>> + O_WRONLY	#1	Open for writing only.(쓰기 전용)
>>> + O_RDWR	#2	Open for reading and writing.(읽기 쓰기)
>>
>> 이 세 가지 상수 중 하나만 지정해야 합니다.

```
왜 플래그가 세 개인가요? O_RDWR을 없애고 O_RDONLY | O_WRONLY만 사용할 수 없나요?  
    안됩니다, 구현에서 항상 O_RDONLY를 0으로 정의했기 때문입니다.  
→ O_RDONLY | O_WRONLY == O_WRONLY
```

> 플래그는 `|`(비트 와이즈 오어)하면 비트 단위로 연산하기에 read \| write는 그냥 write(read가 0이므로)

### The open(2) system call (2/2)
#### optional flags
- O_APPEND - 쓰기 시마다 파일 끝에 추가합니다.
- O_CREAT - 파일이 없으면 생성합니다.
- O_EXCL - O_CREAT가 지정되었고 파일도 이미 있으면 오류를 생성합니다.
- O_TRUNC 파일이 있으면 길이를 0으로 자릅니다.
- O_NONBLOCK 비차단 파일 열기.

플래그 옵션에는 APPEND, CREAT, EXCL, TRUNC 등이 있고, NONBLOCK은 필요 없으니 생략합니다.  
+ 어펜드는 파일 끝에 write할 때 어펜드(추가)합니다.  
+ 크리에이트는 존재하지 않는 파일을 만들 때 씁니다.  
+ 익스클루시브는 제너레이트 에러(생성 오류), O_크리에이트 지정 했을 때 파일이 이미 존재하면 에러를 생성합니다.  
+ 트런크는 오픈할 때 파일이 이미 존재하면 다 지웁니다.

#### mode
- O_CREAT 플래그와 함께 사용만 가능
- 파일 보안 권한(File security permission)

```c
fd = open(“/tmp/newfile”, O_WRONLY|O_CREAT, 0644);
/* if isExist(file) “file open” else “file create & open” */ 

fd = open(“/tmp/newfile”, O_WRONLY|O_CREAT|O_EXCL, 0644);
/* if isExist(file) “open error” else “file create & open” */

fd = open(“/tmp/newfile”, O_WRONLY|O_CREAT|O_TRUNC, 0644);
/* if isExist(file) “file truncate & open ” else “file create & open” */
```
* 마지막 숫자는 퍼미션입니다.

#### example
```c
#include <stdlib.h>	/* exit() */
#include <fcntl.h>	/* open() */

char *workfile = “junk”;

main()
{
    int fd;
    if( (fd = open(workfile, O_RDWR)) == -1)
    {
        printf(“Couldn’t open %s\n”, workfile);
        exit(1);
    }

    exit(0);
}
```

* 대부분의 표준 포함 파일(standard include files)은 일반적으로 /usr/include 디렉토리에 있음
* 프로세스당 20개의 열린 파일
* 하지만 오늘날 대부분의 UNIX 시스템은 20개 이상을 제공함

오픈할 때 파일 컨트롤 헤더는 인클루드(include) 필수입니다.  
exit(엑시트)는 써도 되고 안 써도 됩니다.  
엑시트는 실행을 종료할 때 사용하는데, 이걸 쓰려면 스탠다드 라이브러리 헤드`(stdlib.h)` 인클루드가 반드시 필요합니다.  
`workfile`은 캐릭터 타입 junk라는 문자열(파일네임)에 대한 포인터입니다.  
`정크(junk)`라는 파일을 read write로 open합니다.(있으면 성공, 없으면 실패)  
이 때 fd가 –1이라면(실패하면) 에러 메시지를 출력하고, exit(1)로 빠져나갑니다.

## File open flag
![추가그림1](https://ji-hun-park.github.io/assets/images/image01.png "추가그림1"){: .align-center}
+ 맨 앞의 것은 경로 밑 파일명
+ 파일이 있으면 true, 없으면 false
+ 맨 뒤 숫자는 permission(퍼미션, 권한)
+ 시스템 콜이 실패하면 fd가 음수로 나옴

1. 첫 번째는 파일이 있으면 열고 없으면 에러
2. 두 번째는 파일이 있으면 열고 없으면 파일을 새로 만들고 열기
3. 세 번째는 파일이 있으면 오픈 에러, 없으면 만들고 열기, 없을 때만 실행
4. 네 번째는 파일이 있으면 기존 데이터 지우고 오픈 없으면 만들고 열기
5. 다섯 번째는 파일이 있으면 기존 내용 뒤에 라이트하면서 열기, 없으면 에러

## File permissions
![추가그림2](https://ji-hun-park.github.io/assets/images/image02.png "추가그림2"){: .align-center}
유닉스는 멀티 유저 시스템이라 유저가 굉장히 많아서 유저를 그룹화 했습니다.  
어떤 유저는 어떤 그룹에 속합니다.(여러 그룹에 속할 수도 있지만 보통은 한 개의 그룹에 속함)  
user, group, others 퍼미션 모두 3비트입니다.

>r은 readable(읽을 수 있는 권리)  
w는 writeable(쓸 수 있음)  
x는 executable(실행 파일이라면 실행해서 프로세스 만들 수 있음)

>유저는 파일의 오너  
그룹은 파일의 유저가 속한 그룹에 있는 다른 유저들  
아더스는 타 그룹

- 맨 앞에 –는 레귤러 파일임을 나타냄
- ls –l test 하면 `test`란 파일의 정보를 봄
- 첫 글자(파일 타입) 뒤에 9자리는 3개씩 끊어서 봄
    - -는 권한 없음(자신도 권한 없으면 안 됌)
    - 특정 문자는 해당 권한 있음
    - 그 뒤에 나오는 이름이 오너
    - 오너 뒤가 그룹명
- 퍼미션은 `bit(비트)`로 이루어져 있기에 숫자로 표현 가능
    - 앞에서부터 차례대로 표현
    - 0과 1로 표현
        - 0은 권한 없음
        - 1은 권한 있음
- 8진수로 표시 가능
    - 764 = 111 110 100 = rwx rw- r--
        - 유저(읽기,쓰기,실행) 그룹(일기, 쓰기) 나머지(읽기)
- 파일의 오너가 퍼미션을 체인지 모드로 변경 가능
    - chmod u+x test – 유저에다 익스큐트 퍼미션 추가
    - chmod g-r test – 그룹에다 리드 퍼미션 삭제
    - chmod o+w test – 아더스에 라이트 퍼미션 추가
- 혹은 숫자로 한꺼번에 변경 가능
- 파일의 오너만 퍼미션 변경 가능

## Symbolic names for file permissions 
![그림05](https://ji-hun-park.github.io/assets/images/그림28.jpg "그림05"){: .align-center}

+ 파일 퍼미션을 디모닉한 심볼 네임으로 주기도 함(잘 안 씀)
    + 숫자로 쓰는게 편함
+ 앞에다 S_를 항상 쓰게 되어있음
+ `fcntl.h` 헤더에 들어있음
+ 공통적으로 `I`가 들어감

```c
int fd; 
mode_t fdmode = ( S_IRUSR | 
		  S_IWUSR | 
		  S_IRGRP | 
		  S_IROTH );

fd = open(“file”, O_RDWR | O_CREAT, fdmode);
```

## The creat(2) system call
```c
#include <fcntl.h> 

int creat(const char *pathname, mode_t mode);


//Returns: file descriptor if OK, -1 on error 
```
패스네임을 주면 그 이름의 파일을 ,뒤에 퍼미션을 주고서 만듭니다.  
모드(mode) 부분에 4자리 숫자로 퍼미션을 주면 됩니다.  
mode_t(unsigned int)는 프로미티브 시스템 데이터 타입입니다.  
반환(return)은 파일 디스크립터가 리턴됩니다.(에러일 경우 –1 리턴)

- 파일을 만드는 또 다른 방법.
- 파일이 이미 존재하는 경우 두 번째 인자는 무시됩니다.
- open과 달리 creat는 파일 서술자를 반환하기 전에 항상 기존 파일을 잘라냅니다.
- creat는 항상 쓰기 전용으로 파일을 엽니다.

```c
fd = creat(“/tmp/newfile”, 0644);

==

fd = open(“/tmp/newfile”, O_WRONLY|O_CREAT|O_TRUNC, 0644);
```
- 위의 코드 두 줄은(맨 위와 맨 아래) 서로 같은 명령어

이미 있을 땐 잘라버리고 열어 버리고, 없을 때는 새로 생성해서 엽니다.  
크리에이트는 항상 트런케이트 합니다.  
항상 라이트 온리로 오픈 합니다.  

## Owner and permission of a new file
### When you create a new file (open or create)
새로운 파일이 오픈이나 크리에이트해서 만들어지면 부모(parent) 디렉토리에 새로운 이름이 쓰여집니다.  
즉 이를 위해 부모 디렉토리의 라이트 퍼미션이 필요합니다.(이는 올라 올라 루트까지 다 있어야 합니다.)  
디렉토리는 안에 있는 파일들의 목록을 지닌 파일일 뿐입니다.

### Who owns it?
누가 오너(소유자)인가?  
프로세스 유저 아이디에는 `effective(이펙티브)`랑 `real(리얼)` 2가지가 있습니다.(그룹 아이디도 마찬가지)  
소유자는 프로세스의 유효(effective) 사용자 ID로 설정됩니다.  
그룹은 프로세스의 유효(effective) 그룹 ID로 설정됩니다.  
이는 파일을 만들 때 프로세스가 만듭니다.

## The close(2) system call
```c
#include <unistd.h>

int close(int filedes);


//Returns: 0 if OK, -1 on error 
```
간단한 클로즈 시스템 콜입니다.  
파일 디스크럽트(파일 서술자)가 `filedes` 자리에 들어갑니다.  
열린 파일은 close로 닫힙니다.  
전체적인 혼란을 방지하기 위해, 프로그램이 실행을 완료(종료)하면 close를 안해도 모든 열린 파일이 자동으로 닫힙니다.
```c
fd = open(“/tmp/newfile”, O_RDONLY);
	.
	.
	.
fd = close(fd);
```

## The read(2) system call
### The read(2) system call (1/2)
```c
#include <unistd.h>

ssize_t read(int filedes, void *buffer, size_t n);


//Returns: number of bytes read, 0 if end of file, -1 on error
```
파일을 읽으면 현재 파일 위치에서 메모리로 바이트가 복사되고, 그런 다음 파일 위치가 업데이트됩니다.  
- Arguments(인자들)
    - filedes
        - 파일 서술자(open 또는 creat에서 얻음)
    - buffer
        - 데이터가 복사될 배열이나 구조에 대한 포인터입니다.
    - n
        - 파일에서 읽을 바이트 수입니다.

+ 캐릭터 타입 포인터 buffer(void면 타입을 미리 정하지 않음, 무조건 포인터가 들어가야 함!)
    * 메모리의 시작 위치를 가리키는 변수
+ n이 가리키는 byte의 수만큼 읽어서
    + 파일 디스크립트(파일 서술자)가 가리키는 파일에서
        + 버퍼가 가리키는 시작 주소부터
            + n만큼 넣으라는 의미
+ size_t는 프리미티브 시스템 데이터 타입(인티져(int) 정도)
+ 리드의 리턴은 실제로 읽은 바이트의 수가 리턴됨(실패시 –1 리턴)
+ 파일의 마지막에 도착한 상태에서 읽으면 하나도 못 읽어서 0 리턴`(중요!)`

### The read(2) system call (2/2)
```c
int fd;
ssize_t n1, n2, n3;
char buf1[512], buf2[512], buf3[512];

if( (fd = open(“foo”, O_RDONLY)) == -1)
    return -1;

			        /* f_offset : 0 */
n1 = read(fd, buf1, 512);	/* n1 : 512, f_offset : 512 */
n2 = read(fd, buf2, 512);	/* n2 : 188, f_offset : 700 */
n3 = read(fd, buf3, 512);	/* n3 : 0, when read EOF */
```
![그림06](https://ji-hun-park.github.io/assets/images/그림29.jpg "그림06"){: .align-center}
- File descriptor table에서 File Table을 가리키고, File Table에서 File을 가리킵니다!

파일은 오픈할 때 `파일 포지션(파일 오프셋)`이 정해집니다.(대개는 맨 앞, `어펜드`면 맨 뒤를 가리킨다)  
현재 읽을 위치를 가리킵니다.  
n(읽을 데이터의 개수)만큼 읽고나면 파일 포지션이 n의 마지막으로 이동하고, 읽을 때마다 위치를 이동합니다.  
파일 포지션은 리드나 라이트할 때 그걸 실행할 위치를 가리킵니다.(읽거나 쓴 만큼 다음 포지션이 이동합니다.)

#### 코드 설명
ssize_t(int) 타입 3개, 캐릭터 타입 버퍼 3개(사이즈 512)가 있습니다.  
현재 디렉토리 밑에 `foo`라는 이름의 파일을 read only(읽기 전용)로 읽습니다.  
반환된 값이 –1, 즉 읽지 못 했을 때(파일이 없을 때나 읽을 권한이 없을 때) -1을 리턴합니다.  
fd에 3이 들어가고, 이를 read에 인자로 주면 3번 파일에서 512byte만큼 읽습니다.  
푸의 사이즈는 700byte입니다.  
파일 오프셋(포지션)은 처음에는 시작 위치(0)를 가리키고 512 byte만큼 읽으면, 512바이트만큼 뒤로 오프셋을 변경합니다.  
이 때 512를 읽으라고 명령하면 남은 188byte만큼만 읽습니다.  
이 때 700 위치를 가리킵니다.`(EOF, 엔드 오브 파일)`  
첫 번째 위치가 0, 마지막 위치는 699입니다.(700이 아님!)  
700이 가리키는 곳은 데이터가 없습니다.(End of File)  
`n2`가 끝나면 700을 가리키게 되고, 마지막은 읽을 게 없어서 0이 리턴됩니다.

#### 이미지 설명
[이미지](https://ji-hun-park.github.io/linux/Linux-Theory-02/#the-read2-system-call-22)는 커널 안의 구조(데이터 스트럭쳐)입니다.  
유저 프로그램에서 파일을 오픈하면 파일 디스크립터 테이블이 생성됩니다.  
3번에 `foo`라는 파일을 가리키는, 파일 테이블을 가리키는 포인터가 테이블에 추가됩니다.  
파일 테이블 데이터 스트럭쳐에서 마지막은 파일의 위치를 가리킵니다.  
읽을 때마다 파일 오프셋이 업데이트 됩니다.  
타일 테이블을 가리키는 디스크립터가 하나라는 제약은 없습니다.  
카운트는 자신을 가리키는 포인터의 수입니다.

## The write(2) system call
```c
#include <unistd.h>

ssize_t write(int filedes, const void *buffer, size_t n);

//Returns: number of bytes written if OK, -1 on error
```
* 파일을 쓰면 메모리에서 현재 파일 위치로 바이트가 복사되고, 현재 파일 위치가 업데이트됩니다.  
>Arguments(인자)
>>filedes: 파일 설명자(open 또는 creat에서 얻음)  
buffer: 쓸 데이터에 대한 포인터  
n: 쓸 바이트 수

+ 프로그램이 기존 파일을 쓰기 위해 여는 경우
    - 파일의 이전 데이터는 문자 단위로 새 파일에 의해 덮어쓰여집니다.  
+ open에서 O_APPEND 옵션이 지정된 경우 파일의 오프셋은 현재 파일 끝으로 설정됩니다.

### 예제
```c
int copyfile ( const char *name1, const char *name2){
    int infile, outfile;
    ssize_t nread;
    char buffer[BUFSIZE];	/* BUFSIZE : 512 */

    if ( (infile = open (name1, O_RDONLY ) ) == -1)
	return(-1);

    /* FMODE : O_WRONLY｜O_CREAT｜O_TRUNC */
    if ( (outfile = open (name2, FMODE, PERM) ) == -1){
        close (infile);
        return (-2);
    }
    
    while ( (nread = read (infile, buffer, BUFSIZE) ) > 0){
        if ( write(outfile, buffer, nread) < nread ){
	   close (infile);
	   close (outfile);
	   return (-3); 		/* 쓰기 오류 */
        }
    }

    close (infile);
    close (outfile);

    if ( nread == -1)  return (-4)	/* 마지막 읽기에서 오류 발생 */
    else   return (0); 		        /* 만사가 잘 되었음*/
}
```
`copfile(name1, name2는 문자열)` 네임1을 네임2로 copy(복사)합니다.

`unistd 헤더`가 있어야하고, write 명령어로 매개변수로 fd, buffer, n을 주는건 read랑 같습니다.  
리드에선 파일에서 읽어서 버퍼로 데이터를 집어 넣었으나, 라이트에선 버퍼 안에 메모리를, 버퍼가 가리키는 시작 위치에서 n바이트만큼 파일에다 집어 넣습니다.(리드와 반대)  
`ssize_t`형 `nread`에는 실제로 라이트가 된 데이터의, 바이트의 수가 리턴됩니다.(실패하면 –1 리턴)  
리드와 마찬가지로 현재 파일 위치가 n만큼 뒤로 이동합니다.

프로그램을 오픈했을 때 이미 있는 파일이라면 overwrite(오버라이트, 덮어쓰기)합니다.(기존 내용에 덮어 씌우는데 뒤에 내용은 그대로 남습니다.)  
`APPEND`로 오픈하면 파일 오프셋이 엔드 오브 파일(EOF)이라 맨 마지막부터 라이트합니다.

`infile`, `outfile`은 fd(파일 서술자), `nread`는 실제로 리드한 사이즈입니다.  
`buffer`는 메모리 버퍼, 최대 512바이트입니다.  
name1을 오픈합니다.(실패하면 오류)  
`FMODE`는 있으면 자르고(TRUNC, empty로 만듭니다.) 열고, 없으면 `PERM` 퍼미션을 주고 새로 만들어서 엽니다.(O_WRONLY｜O_CREAT｜O_TRUNC)  
name2도 엽니다.(실패하면 인파일 닫고 오류)

nread에 리드(인파일(네임1),버퍼에 버프사이즈만큼 넣습니다.) 넣고 이 값이 0보다 크면(뭔가를 읽었다면) 라이트(위에서 읽은 만큼 버퍼에 저장해서 아웃파일에 라이트), 이 값이 nread보다 작으면 오류, 크거나 같으면 과정을 반복합니다.  
끝나면 둘 다 close합니다.

다만 nread가 0이 아닌 –1에서 끝나는 경우가 있을 수 있습니다, 그 경우 오류가 발생합니다.  
모든 작업이 무사히 종료되면 0을 반환(return)합니다.

## read, write and efficiency
### read, write and efficiency (1/3)
```c
copyfile(“test.in”, “test.out”);
```
![그림07](https://ji-hun-park.github.io/assets/images/그림30.jpg "그림07"){: .align-center}
오퍼레이팅 시스템(OS)은 프로세스가 하나만 도는 게 아닙니다.  

- 리얼 타임은 모든 걸 실행한 시간  
- 유저 타임은 실제 프로세스를 실행한 시간  
- 시스템 타임은 OS가 실행한 시간

- 버퍼 크기를 늘리면 성능이 향상됩니다.
    - 가장 좋은 성능은 `BUFSIZE`가 시스템의 자연 디스크 차단 계수의 배수일 때 달성됩니다.
    - 자연 디스크 블록: `stat`의 `st_blksize`
- 시스템 호출 수 감소
    - 시스템 호출이 이루어질 때 프로그램과 커널 간의 모드를 전환하는 것은 비교적 비쌀 수 있습니다.

블록 사이즈는 I/O의 단위입니다.(4K가 퍼포먼스가 가장 좋음)  
버퍼 사이즈를 증가 시키면 퍼포먼스가 좋아집니다.(다만 한계가 있음)  
시스템 콜은 컨텍스트 스위칭(문맥 교환)도 발생하고 해서 Heavy해서 실행 횟수를 줄이는게 좋습니다.

### read, write and efficiency (2/3)
#### The write system call is too fast. Why?
write 시스템 호출(system call)을 실행하면 쓰기를 수행한 다음 반환(return)하지 않습니다.  
커널의 버퍼 캐시로 데이터를 전송한 다음 반환합니다. → delayed writing  
(디스크의 경우 커널에 있는 버퍼 캐쉬를 거칩니다, 이 버퍼 캐쉬가 꽉 찰 때까지(4K바이트) 라이팅을 안 합니다.)  

>그러나 디스크 오류가 발생하거나 커널이 어떤 이유로든 중단되면 게임 오버!  
>>`썼던` 데이터가 디스크에 전혀 없다는 것을 알게 됩니다.

### read, write and efficiency (3/3)
#### System kernel block
![그림08](https://ji-hun-park.github.io/assets/images/그림31.jpg "그림08"){: .align-center}
- 참고만 하기

## The lseek(2) system call
```c
#include <unistd.h>
/* the character l in the name lseek means “long integer” */
off_t lseek(int filedes, off_t offset, int start_flag);

//Returns: new file offset if OK, -1 on error
```
열려 있는 파일의 오프셋은 lseek를 호출하여 명시적으로 설정할 수 있습니다.  
`파일 오프셋`은 일반 파일에서 다음 읽기(read) 또는 쓰기(write)가 발생할 위치를 표시하는 위치입니다.  
- Arguments(인자들)
    - filedes: file descriptor(파일 서술자, open, creat의 반환값으로 얻을 수 있음)
        - 파이프, FIFO 또는 소켓을 참조하는 경우 errno(에러 넘버)를 ESPIPE(return -1)로 설정합니다.
    - offset: start_flag로부터의 바이트 수
    - start_flag: starting position(시작 위치)
        - SEEK_SET #0
        - SEEK_CUR #1
        - SEEK_END #2

![그림09](https://ji-hun-park.github.io/assets/images/그림32.jpg "그림09"){: .align-center}
`unistd.h`가 반드시 include(인클루드, 포함) 되어야 합니다.  
파일 오프셋을 바꿔주는 역할입니다.
```c
off_t nepos;

newpos = lseek(fd, (off_t)-16, SEEK_END);

----------------------------------------------------------------------------

fd = open(fname, O_RDWR);
lseek(fd, (off_t)0, SEEK_END);
write(fd, outbuf, OBSIZE);

	    ==

fd = open(fname, O_WRONLY|O_APPEND);
write(fd, outbuf, OBSIZE);

----------------------------------------------------------------------------

off_t filesize;
int filedes;

filesize = lseek(fd, (off_t)0, SEEK_END); /* filesize is the size of file */
```
### 코드 설명
off_t(프리미티브 시스템 데이터 타입, int나 long)  
lseek(fd(오픈이나 크리에이트해야함),오프셋,스타트_플래그)  
Disk(디스크, 랜덤 엑세스(접근) 가능한) 레귤러 파일에서만 사용 가능합니다.

파일 오프셋(file offset)은 다음에 읽거나 쓸 위치를 표시한 것입니다.  
오프셋(offset)은 +-를 가지고, 스타트_플래그부터 오프셋(offset)만큼 +면 앞으로 –면 뒤로 이동합니다.

스타트 플래그는 3가지 값을 가질 수 있습니다.

>시크_셋(SEEK_SET) 0, 파일의 시작 위치를 기준, 이전으로 갈 수 없음  
시크_커(SEEK_CUR) 1, 현재의 파일 오프셋 기준  
시크_엔드(SEEK_END) 2, 파일의 마지막 기준, 이후로 갈 수 있음

`newpos`에 lseek를 넣는데, 내용은 fd번 파일에, 16만큼 전으로, 엔드 오브 파일(EOF)로 파일 오프셋 이동입니다.

`fname` read write only(읽기 쓰기 전용)로 엽니다.  
엘시크(lseek)로 엔드 오브 파일(EOF)로 오프셋을 이동시킵니다.  
write로 fd에 `OBSIZE`만큼 `outbuf`에 넣어서 라이트합니다.  
이건 파일을 라이트온리(쓰기 전용) 어펜드로 열어서 라이트하는 것과 같은 기능입니다.(경계선 안에 == 전후 코드들)

`filesize`에 lseek(fd,0,SEEK_END)를 넣으면 오프셋이 `마지막`을 가리키는데, 그 값이 파일의 `사이즈`입니다.

아래는 참고만  
```c
02_append.c
02_append_lseek.c
02_append_end.c
/****************************** append ******************************/
$ cat > 02_append_file
xxxxx^D
$ ./append 02_append_file &
[1] 9903
Press any key!!
$  ./append_end 02_append_file 
Press any key!!
End : Press any key!!
[1]+  Stopped                 ./append 02_append_file
$ cat 02_append_file 
xxxxxeeeee
$ fg 1
./append 02_append_file
$ cat 02_append_file
xxxxxeeeeeaaaaa
/****************************** append ******************************/

/****************************** lseek ******************************/
$ cat > 02_append_file
xxxxx^D
$ ./append_lseek 02_append_file &
[1] 9903
Press any key!!
$  ./append_end 02_append_file 
Press any key!!
End : Press any key!!
[1]+  Stopped                 ./append 02_append_file
$ cat 02_append_file 
xxxxxeeeee
$ fg 1
./append_lseek 02_append_file
$ cat 02_append_file
xxxxxaaaaa
/****************************** lseek ******************************/
```
## File Share
### File Share (1/4)
- 모든 프로세스는 `프로세스 테이블(process table)`에 엔트리(entry)를 가지고 있습니다.
- 각 프로세스 테이블 엔트리 내에는 열린 파일 서술자 테이블이 있습니다.
    - 파일 서술자 플래그
    - 파일 테이블 엔트리에 대한 포인터

- 커널은 모든 열린 파일에 대한 `파일 테이블(file table)`을 유지 관리합니다. 각 파일 테이블 엔트리에는 다음이 포함됩니다.
    - 파일의 파일 상태 플래그(읽기, 쓰기 등)
    - 현재 파일 오프셋
    - 파일의 v-노드 테이블 엔트리에 대한 포인터

- 열려 있는 각 파일(또는 장치)에는 파일 유형에 대한 정보와 파일에서 작동하는 함수에 대한 포인터가 포함된 v-노드 구조가 있습니다.

파일을 엑세스할 때 공유하면서 엑세스 가능합니다.  
유저가 프로세스를 생성하면 커널 안에 있는 프로세스 테이블(process table)이라는 데이터 스트럭쳐(자료 구조)에 엔트리 하나가 새로 생성됩니다.  
이 엔트리 안에 해당 프로세스의 각종 정보가 들어있습니다.(파일 디스크립터 테이블, 프로세스 하나 당 한 개가 존재함)

### File Share (2/4)
![그림10](https://ji-hun-park.github.io/assets/images/그림33.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림34.jpg "그림11"){: .align-center}
파일을 오픈하면 커널에 의해 그 파일에 대한 파일 테이블이 생성됩니다.(프로세스에 종속되지 않음)  
각각의 파일 테이블은 엔트리를 가지고 있습니다.  
현재 오픈한 파일에 대해, 파일을 뭐로 오픈 했는지(리드온리,라이트온리,리드라이트 등등), 현재 파일 오프셋, 파일이 실제로 가리키는 커널이, 실제로 가리키는 v-node(i-node) table 등이 있습니다.

v-node(i-node) table은 파일과 1:1 대응합니다.  
어떤 파일이 존재하면 그에 대응하는 브이(아이)노드 테이블은 단 `하나`입니다!

### File Share (3/4)
```c
$ ./a.out	/* fd = open(“test”, O_RDONLY); */

$ ./b.out	/* fd = open(“test”, O_RDONLY); */
```
![그림12](https://ji-hun-park.github.io/assets/images/그림35.jpg "그림12"){: .align-center}
특정 파일에 엑세스하는 파일 테이블은 여러 개가 있을 수 있습니다.  
모든 오픈 파일은 파일 테이블을 통해 v-노드 스트럭쳐(그 안에 i-노드 스트럭쳐)를 가리키면 특정 파일만 지정합니다.  
a.out이란 프로세스가 만들어지면 프로세스 테이블에 그에 대한 엔트리가 하나 생성됩니다.

### File Share (4/4)
![그림21](https://ji-hun-park.github.io/assets/images/그림44.jpg "그림21"){: .align-center}
그 엔트리 안에 파일 디스크립터(서술자) 테이블이 들어갑니다.  
v(i)-노드는 파일을 직접적으로 가리키고 유일하나, 파일 테이블은 그 v노드 테이블을 가리킬 뿐 여러 개가 있을 수 있습니다.(fd 1과 fd 2는 별개의 파일 테이블이지만 같은 v노드를 가리킵니다, 공유(스크린))  
서로 다른 프로세스가 같은 파일을 공유할 수 있습니다.

- `a.out`, `b.out` 각각의 프로세스의 프로세스 테이블 안에 파일 디스크립터(서술자) 테이블 안에 파일 테이블이 있고, 같은 fd를 가지고 같은 v-노드를 가리키면 그 v-노드가 가리키는 파일은 유일합니다!(1대 1 대응)
    - 이 경우 서로 다른 프로세스가 같은 파일을 공유함

fd 3, fd 4, buf\[20\](size20짜리 buffer)를 선언하고, 둘이 같은 파일(`file`)을 오픈합니다.  
한 개의 프로세스 안에서 2개의 파일 디스크럽터(서술자)가 같은 파일을 공유합니다.  
fd 3에서 20을 읽어서 오프셋이 20으로 바뀌어도 fd 4는 오프셋이 그대로입니다.  
fd 4에서 읽어서 오프셋이 30으로 바뀌어도 fd 3의 오프셋은 그대로입니다.

close하면 `해당 파일의 테이블`만 없어집니다.  
`v-노드`는 파일이 디스크 안에 `존재`하면 절대적으로 `존재`합니다!(close해도 안 없어짐!)

## The dup(2)and dup2(2)system call
```c
#include <unistd.h>

int dup(int filedes);
int dup2(int filedes, int filedes2);

//Both return: new file descriptor if OK, -1 on error
```
기존 파일 서술자는 다음 함수 중 하나에 의해 복제됩니다.
```c
newfd = dup(1) /* STDOUT_FILENO #1 */
	==
newfd = fcntl(1, F_DUPFD, 0);
	!=
newfd = 1
```
![그림14](https://ji-hun-park.github.io/assets/images/그림37.jpg "그림14"){: .align-center}
`dup2`는 파일 디스크립터(앞에 걸)를 복제(뒤에 걸로)합니다.  
`dup`은 파일디스크립터가 새로 카피(복제)되어 오픈(lowest unused integer로)됩니다.

예제 – dup(1)으로 fd 1을 새로운 fd 3으로 카피해서 만들었고, 이 경우 파일 테이블은 동일합니다.  
즉, 같은 파일 테이블을 2개의 파일 포인터가 가리키므로 파일 스테이터스 플래그가 2입니다.  
이는 `파일 컨트롤(fcntl)`의 커멘드 `둡 파일 디스크(F_DUPFD)`랑 같습니다.  
1번을 0보다 큰 번호 중 안 쓰이는 가장 작은 번호로 카피합니다.

### Example : dup2(2)
```c
-----------------------------------------------
|fd4 = open(“test”, O_RDONLY); /* fd4 == 4  */|
-----------------------------------------------
dup2(3, fd4);	/* test’s file descriptor closed */

==

close(fd4);
fcntl(3, F_DUPFD, fd4)
```
![그림15](https://ji-hun-park.github.io/assets/images/그림38.jpg "그림15"){: .align-center}
*기존에 fd3 = open("file", O_RDWR); 실행한 상황  
둡2 – 본래 fd4는 `test`라는 파일을 가리키고 있었는데, 이걸 오픈하고 fd3의 내용을 fd4에 카피(포인터가 가리키는 시작 주소의 변경)하면 기존에 가리키던 포인터가 끊어집니다.(count가 0이므로 test의 파일 서술자가 종료됩니다.)  
둘은 결과적으로 같은 v-node table(같은 파일)을 가리킵니다.  
`test`의 파일 테이블의 count는 1에서 0이 되고, 새로운 file table의 count는 1에서 2가 됩니다.

## The fcntl(2) system call
### The fcntl(2) system call (1/2)
```c
#include <fcntl.h>

int fcntl(int filedes, int cmd, ...);

//Returns: depends on cmd if OK (see following), , -1 on error
```
fcntl(파일 컨트롤) 함수는 이미 열려 있는 파일의 속성을 변경할 수 있습니다.
- Arguments(인자들)
    - filedes: 파일 설명자(open 또는 creat에서 얻음)
    - cmd: 프로그래머는 정수 cmd 매개변수에 대한 값을 선택하여 특정 함수를 선택합니다.
        - F_DUPFD: 기존 파일 서술자를 복제합니다.
        - F_GETFD 또는 F_SETFD: 파일 서술자 플래그를 가져오거나 설정합니다 → ch05
        - F_GETFL 또는 F_SETFL: 파일 상태 플래그를 가져오거나 설정합니다.
        - F_GETOWN 또는 F_SETOWN: 비동기 I/O 소유권을 가져오거나 설정합니다 → ch06
        - F_GETLK, F_SETLK 또는 F_SETLKW: 레코드 잠금을 가져오거나 설정합니다 → ch08

파일 컨트롤(시스템 콜) - `fcntl.h`(파일컨트롤.헤드)가 반드시 include(인클루드, 포함) 되어야 합니다.  
엘리먼트1(첫 번째 인자)은 파일 디스크립터(서술자), 엘리먼트2(두 번째 인자)는 명령어, 세 번째 아규먼트(인자)는 안 들어갈 수 있습니다(옵션).  
실패하면 –1이 리턴되는 것은 앞이랑 같습니다.  
성공하면 명령어에 따라 리턴 값이 달라집니다.

각각의 명령어 표준의 대한 상세한 설명은 `susv4.zip`에 있습니다.  
DUPFD – 둡과 같은 기능입니다.  
F_GETFL 또는 F_SETFL – 겟 / 셋 파일 스테이터스(상태) 플래그스(fcntl(1,dup,0))

### The fcntl(2) system call (2/2)
```c
#include <fcntl.h>

int filestatus(int filedes)
{
  int arg1;

  if (( arg1 = fcntl (filedes, F_GETFL)) == -1)
  {
    printf ("filestatus failed\n"); 
    return (-1);
  }

  printf ("File descriptor %d: ",filedes);

  /* 개방시의 플래그를 테스트한다. */
  switch ( arg1 & O_ACCMODE){
    case O_WRONLY: printf ("write-only"); break;
    case O_RDWR:   printf ("read-write"); break;
    case O_RDONLY: printf ("read-only");  break;
    default: printf("No such mode");
  }

  if (arg1 & O_APPEND)
    printf (" -append flag set");

  printf ("\n");
  return (0);
}
```
![추가그림3](https://ji-hun-park.github.io/assets/images/image03.png "추가그림3"){: .align-center}
예제 – fd만으로 해당 파일이 어떤 권한으로 오픈되었는지를 알아보는 코드

어떤 파일디스크립터를 리턴을 받습니다(그 이전에 파일을 오픈한 상태).  
`F_GETFL`(파일 겟 스페이터스 플래그스) 명령을 주면(3번째 아규먼트(인자)는 필요 없음), 리턴 값이 –1이면 실패하고 아니면 열린 정보를 리턴합니다(arg1에 이 정보 저장).

switch문으로 arg1에서 엑세스 모드와 & 연산해 결과에 따라 출력합니다.(이에 관한 정보는 fcntl.h에 저장되어 있음)  
#define O_ACCMODE 3(미리 정해진 값), 이걸 &(비트와이즈앤드)연산해서,  
그 결과 값으로 라이트온리인지 리드라이트인지 리드온리인지 판단합니다.

O_APPEND랑 &할 시 해당 위치에서 연산해 어펜드 플래그가 셋 되어있는지 판단합니다.  
파일 컨트롤의 겟 스테이터스 플래그 명령어를 써서 플래그들을 알 수 있습니다.

# 2.2 Standard input, standard output and standard error

## Redirection
### Redirection (1/3)
```c
#include <stdio.h>
#include <unistd.h>

int main()
{
	char buf[BUFSIZ];
	while( read(0, buf, BUFSIZ) )
	{
		printf("%s", buf);
	}
	return 0;
}
```
![그림16](https://ji-hun-park.github.io/assets/images/그림45.jpg "그림16"){: .align-center}
파일 서술자 0, 1을 오픈하지 않아도 리드, 라이트가 가능합니다.

`< infile(파일명)`은 키보드 입력, Standard Input(스탠다드 인풋, 표준 입력)을 파일로 리다이렉션(Redirection, 방향 변경)합니다.  
dup2 infile,0로 키보드를 가리키는 fd가 infile(fd 변수 이름, 3)을 가리키게 바꿔줍니다.  
프로그램은 그것도 모르고 바보같이 파일에서 읽어들입니다!  
이것이 바로 `Input Redirection(인풋 리디렉션, 입력 리디렉션)`입니다!

### Redirection (2/3)
![그림17](https://ji-hun-park.github.io/assets/images/그림40.jpg "그림17"){: .align-center}
write(1,buffer,n) - 버퍼에 있는 내용을 N바이트만큼 스크린에 라이트합니다.  
좌 파일명, 우 변수명, `> 파일명`으로 output(아웃풋, 출력)합니다.  
리다이렉션 명령을 쉘이 사전적으로 수행합니다.  
전에는 standard output(스탠다드 아웃풋, 표준 출력), 스크린(모니터)을 가리키고 있던 fd 1번이 overwrite(오버라이트, 덮어쓰기)되면서 파일을 가리키게 됩니다!  
프로그램은 바뀐 줄도 모르고 멍청하게 실행해서 `screen(스크린, 화면)`으로 나오는게 아닌 `outfile`로 나오게 만듭니다!  
이것이 바로 `Output Redirection(아웃풋 리디렉션, 출력 리디렉션)`입니다!

### Redirection (3/3)
```c
prog_name < infile > outfile
```
(<>는 delimiter(딜리미터, 구획 문자))이렇게하면 인 리다이렉션과 아웃 리다이렉션을 동시에 할 수 있습니다! 해석은 쉘이 합니다.  
- infile에서 읽어들여 outfile로 보내기

## io example
```c
#include <stdlib.h>
#include <unistd.h>

#define SIZE 512

main()
{
  ssize_t nread;
  char buf[SIZE];
  
  while ( (nread = read (0, buf, SIZE)) > 0)
     write (1, buf, nread);
  exit (0);
}
```
```c
$io
This is line 1 ↓
This is line 1
This is line 2 ↓
This is line 2
<Ctrl-D>
$
```
- 각 줄은 Return 키를 누르면 출력됩니다.
- 이는 터미널에서 데이터를 받는 데 사용되는 read가 일반적으로 각 줄바꿈 문자 다음에 반환(return)되기 때문입니다.

`stdlib.h`(스탠다드립)은 exit, `unistd.h`(유니스트드)는 ssize_t를 위해 필요합니다.  
0,1은 오픈할 필요가 없습니다!

리턴 다음 널캐릭터(NULL) 포함 16자를 리턴하고, 라이트로 버퍼에 있는 값을 보여줍니다  
입력값이 0보다 크면 반복합니다.  
컨트롤(Ctrl)과 D를 동시에 누르면 엔드오브파일(EOF)이 되면서 0이 리턴되어 프로그램이 종료됩니다.

# 2.3 The Standard I/O Library: a look ahead
스탠다드 아이/오 라이브러리는 시스템 콜은 아닙니다.  
그러나 라이브러리에 있는 명령어들의 내용을 보면 입력과 관련된 것 들은 다 read를 사용합니다.

## The Standard I/O Library (1/3)
### UNIX I/O (system call)
유닉스 입출력 시스템 콜(리드, 라이트, 오픈)은 간단한 시퀀스의 바이트를 처리합니다.  
프로그래머에게 복잡한 많은 것은 스탠다드 아이/오로 남겨뒀습니다.  
Efficiency(이피션시, 효율성) 고려는 Developer(디벨롭퍼, 개발자)가 알아서 해야합니다.
### Standard I/O
표준 입출력 – 자체 자동 메모리 버퍼가 있습니다.  
사용자에게 친숙한 인터페이스로 좀 더 쓰기 좋습니다.  
라이브러리를 사용하기 쉽게 합니다.  
프로그래머는 이피션시(효율성)를 걱정할 필요가 없어집니다.

## The Standard I/O Library (2/3)
### Difference between Standard I/O and UNIX I/O
- 파일이 서술되는 방식
    - FILE*(파일 포인터) 또는 파일 서술자

### Standard I/O routines are written around the system call primitives
```c
#include <stdio.h>
#include <stdlib.h>

main()
{
 	FILE *stream;

 	if ( ( stream = fopen ("junk", "r")) == NULL)
 	{
 	   printf ("Could not open file junk\n");
 	   exit (1);
 	}
}
```
- predefined file pointer(사전 정의된 파일 포인터)
    - stdin  표준 입력
    - stdout  표준 출력
    - stderr  표준 오류

#### 설명
- fd대신 파일 포인터 사용
- open은 시스템 콜
- fopen은 라이브러리
- fopen은 리드온리 등등이 아닌 r,w,a 등 리모닉한 걸 쓴다
- 리턴(return, 반환)되는 것은 fd가 아닌 `파일의 포인터`다

## The Standard I/O Library (3/3)
C 표준 라이브러리(libc.a)에는 상위 수준(higher-level) 표준 I/O 함수 컬렉션이 포함되어 있습니다.  
libc.a를 사용할 것입니다(getc,printf)

- 표준(Standard) I/O 함수(Function)의 예:
    - 파일 열기 및 닫기(fopen 및 fclose)
    - 바이트 읽기 및 쓰기(fread 및 fwrite)
    - 텍스트 줄 읽기 및 쓰기(fgets 및 fputs)
    - 포맷된 읽기 및 쓰기(fscanf 및 fprintf)

>fopen, fclose – 시스템 콜이 아닌 라이브러리(파일 포인터 리턴)  
fread, fwrite(리드, 라이트), fgets, fputs(스트링 리드, 스트링 라이트)  
포맷티드 리딩과 포맷티드 라이팅(fscanf, fprintf) - 파일에 스캔,프린트

## fopen(3)
*앞에서 말했듯 (3)은 라이브러리
```c
#include <stdio.h>

FILE *fopen(const char *restrict pathname, const char *restrict type);

//All three return: file pointer if OK, NULL on error
```
- Arguments(인자들)
    - type:
        - r or rb &nbsp;&nbsp;&nbsp;&nbsp; 읽기 위해 열기  
        - w or wb	&nbsp;&nbsp;&nbsp;&nbsp; 0 길이로 자르거나 쓰기 위해 만들기  
        - a or ab	&nbsp;&nbsp;&nbsp;&nbsp; append; 파일 끝에서 쓰기 위해 열기, 또는 쓰기 위해 만들기  
        - r+ or r+b or rb+ &nbsp;&nbsp;&nbsp;&nbsp; 읽기 및 쓰기 위해 열기  
        - w+ or w+b or wb+ &nbsp;&nbsp;&nbsp;&nbsp; 0 길이로 자르거나 읽기 및 쓰기 위해 만들기  
        - a+ or a+b or ab+ &nbsp;&nbsp;&nbsp;&nbsp; 파일 끝에서 읽기 및 쓰기 위해 열기 또는 만들기


- fopen 리턴 값은 파일의 포인터, 캐릭터 타입 포인터이므로 `type`은 문자열로 들어감
- 실패하면 NULL이 리턴된다
- rb에서 b는 바이트
- 아규먼트(인자)에 관한 자세한 내용은 susv4(싱글 유닉스 스페시피케이션 버젼 4) 참조

## getc(3), putc(3)
```c
#include <stdio.h>

int getc(FILE *istream);

//Return: next character if OK, EOF on end of file or error


int putc(int c, FILE *ostream); 

//Return: c if OK, EOF on error
```
```c
int c;
FILE *istream, *ostream;

/* istream을 읽기전용으로 개방하고, ostream을 쓰기전용으로 개방하라. */
.
.
.
while( ( c=getc(istream)) !=EOF)  //EOF defined in <stdio.h>, -1
        putc(c, ostream);
```

## Buffering
![그림18](https://ji-hun-park.github.io/assets/images/그림41.jpg "그림18"){: .align-center}
- 표준 I/O는 우아한 버퍼링 메커니즘을 통해 이러한 비효율성을 피합니다.
- 사용되는 버퍼는 일반적으로 스트림에서 I/O가 처음 수행될 때 malloc을 호출하는 표준 I/O 함수 중 하나에서 얻습니다.

>밑이 커널 위가 유저 인터페이스  
커널 밑에 버퍼 캐쉬가 있다(디스크같은 블록 디바이스의 4K바이트 채우기)  
라이브러리는 유저 메모리 단계에서 메모리 버퍼 메커니즘을 씀(시스템 콜을 적게 부르기 위해)  
표준 아이/오는 비효율성을 피한다(우아한 버퍼링 메커니즘을 통해)  
메모리 안에 있는 버퍼를 확보할 때는 malloc이라는 시스템 콜 사용

## Writing error message with fprintf(3)
```c
#include <stdio.h> 

int fprintf(FILE *restrict fp, const char   *restrict format, ...); 


//Return: number of characters output if OK, negative value if output error
```
```c
#include <stdio.h>
#include <stdlib.h>

int notfound (const char *progname, const char *filename)
{
    fprintf (stderr, "%s: file %s not found\n", progname, filename);
    exit (1);
}
```
“restrict”(제한)은 동일한 주소가 사용되는 것을 방지합니다.

fprintfile을 대신해서 씁니다.  
리스트릭트가 붙은 것들 끼리는 서로 메모리를 공유하면 안 됩니다!

# 2.4 The errno variable and system calls
errno(에러 넘버, 시스템에서 중요한 변수)

## Error Handling (1/3)
에러 핸들링(시스템 차원에서)

커널에 있는 시스템 콜을 부를 때 오류(Error)가 발생하면 –1을 리턴, 성공하면 0이나 필요한 값들을 리턴합니다.  
-1은 어떤 이유로 실패했는지 알려주지 않습니다.  
시스템 콜이 어떠한 이유로 실패했는지 자세히 알려주는 글로벌 변수가 세팅되어 있습니다.(`errno.h` include하면 `errno`라는 전역 변수(global)에서 미리 정해놓은 상수 값을 세팅해줍니다.)

헤더 파일을 보면 문자 `E`로 시작하는 `상수`들이 `#define`으로 쭉 나열되어 있습니다.  
시스템 콜에서 –1이 리턴되면 즉각 errno가 세팅 됩니다.  
이후 다시 –1이 리턴되면 다시 에러넘버가 세팅이 되고, 이후에 시스템 콜이 성공하면 에러 넘버가 그대로 세팅 됩니다.  
즉, 에러 넘버는 시스템 콜이 실패했을 때 즉시 확인해야 합니다(에러 넘버는 가장 최근의 에러 타입을 저장하기 떄문에).

에러 넘버는 global accessible integer(전역 접근 가능 정수 변수), POSIX는 extern(익스턴, global로 선언) int errno;로 정의, `errno.h`에 있습니다.  
에러 넘버는 오류가 발생하지 않으면 시스템 콜을 실행했을 때 리셋(재설정)되지 않습니다.  
시스템이 실패했을 시 즉각적으로 에러넘버 값을 확인해야 합니다.

## Error Handling (2/3)
![그림19](https://ji-hun-park.github.io/assets/images/그림42.jpg "그림19"){: .align-center}


## Error Handling (3/3)
![그림20](https://ji-hun-park.github.io/assets/images/그림43.jpg "그림20"){: .align-center}


## Error Handling: Wrapper Functions


## 작성중

## 마무리
이상으로 Linux 이론의 파일1편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
