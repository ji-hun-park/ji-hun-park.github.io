﻿---
title:  "Linux 기초 이론 01 Basic"
excerpt: "Linux 기초 이론 첫 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-02-06T23:55:00-23:59
---

# Linux Lecture Theory 01 Basic

## Introduction
### Basic concepts and terminology
이번 시간엔 강의 전반에 걸쳐 사용할 리눅스 기초 지식 및 용어를 간략히 살펴보겠습니다.  
UNIX 파일의 개념을 살펴보는 것으로 시작하겠습니다.

## UNIX Structure
![그림01](https://ji-hun-park.github.io/assets/images/그림19.jpg "그림01"){: .align-center}
`UNIX OS`의 구조입니다.  
가운데가 `커널`(하드웨어를 컨트롤하는 소프트웨어, 오퍼레이팅 시스템의 핵)입니다.  
`System Calls`(시스템 콜스, 커널의 기능을 사용하기 위한 외부 인터페이스, Function API)  
외부 인터페이스는 `Shell`을 거치든 `라이브러리`를 거치든 꼭 `시스템 콜`을 거쳐야 `커널`을 이용 가능합니다.

## Logging In
### Login Name
유닉스는 `멀티 유저`, `멀티 태스킹`이라 로그인이 필요합니다.  
Unix 시스템에 로그인할 때 우선 로그인 이름을 입력합니다.  
다음에 비밀번호가 나옵니다.  
/etc/passwd

### Shells
로그인하면 쉘에 명령을 입력할 수 있습니다.  
%(Prompt), Shell - Command Line Interpreter(CLI, 명령줄 인터프리터, 사용자 입력 인식 및 명령 실행)

```
Bourn shell(/bin/sh), AT&T : $
KornShell(/bin/ksh),  AT&T : $
C shell(/bin/csh),    BSD  : %
```
+ 리눅스 - Bourne Again Shell(바쉘, bash)(KornShell과 비슷하다)

## File and Directories
### FileSystem
유닉스 파일 시스템은 디렉토리와 파일의 계층적 배열입니다.  
모든 것은 이름이 단일 문자 `/`인 root라는 디렉토리에서 시작합니다.  
디렉토리는 디렉토리 항목을 포함하는 `파일`입니다. (디렉토리 항목에는 i-node와 파일 이름이 포함됩니다)  
>디렉토리든 장치든 모든 것이 파일입니다!

### FileName
파일 이름에 나타날 수 없는 문자  
- ‘/’, null 문자(헥사값이 0, 끝을 나타내기에 블링크는 아님)

새 디렉토리가 생성될 때마다 두 개의 파일 이름이 자동으로 생성됩니다: ‘.’, ‘..’
```
. : 현재 디렉토리  
.. : 부모 디렉토리
```

### PathName
![그림02](https://ji-hun-park.github.io/assets/images/그림20.jpg "그림02"){: .align-center}
```
절대 경로명: /usr/keith/file1  
상대 경로명: keith/file1
```

모든 파일 네임은 패스네임을 가집니다.  
절대 경로는 /(루트부터 시작)usr/abc/file1  
루트 외 나머지 / 는 구분자입니다.  
상대 경로는 프람트(쉘)의 Current Working Directory(현재 실행 디렉토리)를 기준으로 합니다.  
현재 실행 디렉토리는 pwd(Print Working Directory) 명령어로 확인 가능합니다.  
상대 경로에서 현재 디렉토리는 적지 않습니다.

### Working Directory
모든 프로세스에는 작업 디렉토리(때로는 현재 작업 디렉토리라고도 함)가 있습니다.  
이 디렉토리에서 모든 상대 경로명이 해석됩니다.
* % pwd(작업 디렉토리 출력)
* $ cd(Change Directory)로 디렉토리 변경 가능합니다.  
```
..으로 이전으로  
/{디렉토리 이름}로 안으로
```

쉘 설정으로 커렌트 워킹 디렉토리를 표시가 가능합니다.

### Home Directory
로그인하면 패스워드를 저장하고, 작업 디렉토리가 홈 디렉토리로 설정됩니다.(/etc/passwd)
>% cd ~ [단숨에 홈 디렉토리로 갈 수 있습니다.]

## Generalization of the file concept
UNIX는 `파일` 개념이 특이합니다.  
`파일` 개념을 확장하여 `Normal File(일반 파일)`, 즉 `Regular File`(UNIX 용어로 일반 파일, 데이터를 포함하고 있는 디스크에 저장되어 있는 파일)뿐만 아니라 `주변 장치` 및 `프로세스 간 통신 채널`도 `파일`에 포함됩니다.

>모든 `I/O 장치`는 `파일`로 표현됩니다.  
/dev/stdin, /dev/stdout, /dev/rmt0  
>$ cat file > /dev/rmt0
>>dev(장치만 모아둠), rmt0(롬 마그네틱 테잎), cat(Catenate)  
cat f1 f2 f3 > f4 (하나로 합치기)  
cat f1 > f5 (복사, 옮기기)  
>>>파일을 장치로 보내기 편합니다.

* 파일로서의 프로세스 간 통신(Inter-Process Communication) 채널:
    - FIFO
    - PIPE
    - SOCKET

프로세스간 통신이 편리합니다.  
`소켓`(인터넷 통신), `파이프`(같은 컴퓨터 안에서 통신), `피포`(파이프의 일종)

## Unix File Types
- Regular file
    - 바이너리나 텍스트 파일
    - 유닉스는 차이를 모릅니다!
- Directory file
    - 다른 파일(하위 파일)의 이름과 위치 정보를 포함하는 파일
- I/O Device(Character Special and Block Special files)
    - 캐릭터 스페셜(1 문자 단위)
        - 한 번 이동하는 단위가 한 Bytes
            - 키보드, 터미널, 프린터, 모니터 등
    - 블록 스페셜(1 블록 단위)
        - 한 번 이동하는 단위가 4K Bytes
            - 디스크, 마그네틱 테이프 등
- FIFO (named pipe)
    - 파이프를 파일 형태로 만든 것, IPC(Inter-Process Communication)를 위한 것
- Socket
    - TCP/IP(네트워크)에서 원격 프로세서 간의 통신 채널

### % ls –al로 볼 수 있는 파일 타입 목록(맨 앞)
```
d	: directory  
l	: symbolic link (바로가기와 비슷하다)  
b	: block special file  
c	: character special file  
p	: FIFO  
s   : socket file  
-       : regular file
```

## Ownership and Permissions
### Ownership
리눅스 멀티 유저, 멀티 태스킹, 공용 서버로 쓸 수 있습니다.  
여러 사람이 쓰는 만큼 각 파일은 특정 사용자(파일 소유자)의 소유권이 있습니다.  
소유자는 파일과 관련된 권한을 선택할 수 있습니다.

### Permissions
접근 권한(자신이 만든 파일을 볼 수 있는 권한)입니다.  
파일에 누가 어떤 방식으로 접근할 수 있는지 제어합니다.  
소유권은 특정 권한을 부여하는데, 그 중 하나는 다른 유형의 파일 속성, 즉 권한을 변경할 수 있는 기능입니다.

## Process
### Process
프로세스란 실행 중인 프로그램의 인스턴스입니다.  
프로그램을 실행시키면 프로세스가 만들어지고, 프로그램을 다룹니다.  
그냥 텍스트(문자) 자체로 Disk(보조 기억 장치)에 잠들어 있으면 프로그램,  
실행돼서 RAM(주 기억 장치) 등에 올라가면 프로세스라고 생각하시면 됩니다.  
프로세스는 한 프로그램에 여러 개가 있을 수 있습니다.

다음은 정보를 볼 수 있는 명령어들 입니다.  
>$ ls [파일들의 리스트 열거]  
>$ ps [프로세스들의 정보 리스트를 봄(자신이 만든 프로세서만)]

* % ls –al (ls는 list, 현재 디렉토리 밑의 파일들을 볼 수 있음)
    + -(옵션) a가 붙으면 .으로 시작하는 히든 파일들도 나타납니다.
    - l은 Long입니다.(상세히 보기)
    * 맨 앞에 나오는 문자 하나가 파일의 타입을 나타냅니다. [파일 타입 보기](https://ji-hun-park.github.io/linux/Linux-Theory-01/#-ls-al%EB%A1%9C-%EB%B3%BC-%EC%88%98-%EC%9E%88%EB%8A%94-%ED%8C%8C%EC%9D%BC-%ED%83%80%EC%9E%85-%EB%AA%A9%EB%A1%9D%EB%A7%A8-%EC%95%9E)
* $ ps –ef (다른 사람들이 만든 프로세스도 볼 수 있습니다.)

### Inter-process communication (IPC)
+ Pipe (프로세스 간 연결 통로)
+ FIFO (Pipe의 일종)
+ Signals (각종 신호들)
+ Shared memory (공유 자원)
+ Semaphore (Synchronization을 위한 툴)
+ Sockets (Network(TCP/IP)를 통해 프로세스 간 원격 통신, 협력(cooperating) 연결점)

## System calls and Library subroutines
### System call
* 소프트웨어 개발자의 UNIX 커널로의 여권입니다.
* 프로그래머는 일반적인 C 서브루틴이나 함수를 호출합니다.
* 커널 내부에서 수행되는 실제 작업입니다.

```c
nread = fread(inputbuf, OBJSIZE, numberobjs, fileptr);
nread = read(filedes, inputbuf, BUFSIZE);
```

![그림03](https://ji-hun-park.github.io/assets/images/그림21.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/그림22.jpg "그림04"){: .align-center}

개발자가 커넬의 인터페이스를 부릅니다.(패스포트, API)  
C 서브로틴이나 펑션을 부를 때처럼 부릅니다.(살짝 다름)  
유저 공간(유저모드)에서 프로그램 코드로 read 유저코드를 부르다가 Kernel space(OS모드)에서 read 커널코드로 바뀝니다.  
fread 라이브러리(시스템 콜 아님)에서 read를 부릅니다.(fread랑 read는 별 차이 없습니다.)

### System call involves two context switches(from user to kernel and back)
유저 모드에서 커널 모드로 바뀌는 것(Trap)은 소프트웨어 Interrupt 방식입니다.  
이때 유저모드는 잠깐 멈춥니다.  
시스템 콜은 헤비합니다.(유저 모드와 커널 모드를 오가면서(사용자에서 커널로, 그리고 다시 사용자로) 두 번의 컨텍스트 스위치스(문맥 전환)가 일어남, Heavy Job)  
프로세스에 자신의 주소 공간을 쓰지 않기 때문에 프로세스 자체 주소 공간 내에서 간단한 함수를 호출하는 것보다 훨씬 더 오래 걸립니다.  
과도하게 시스템 콜을 쓰면 시스템에 과부하가 걸리니 피하는 것이 좋습니다.

### Every system call is defined in a header file.
모든 시스템 호출(시스템 콜)은 헤더 파일에 정의되어 있습니다.  
호출하기 전에 올바른 헤더를 포함했는지 확인해야 합니다.

### Tip!
```
실제로 필요하지 않은 헤더를 포함하는 데는 아무런 해가 없으므로 가장 일반적인 헤더를 마스터 헤더에 수집하여 포함하는 것이 가장 쉽습니다.
```

## 마무리
이상으로 Linux 이론의 기초편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
