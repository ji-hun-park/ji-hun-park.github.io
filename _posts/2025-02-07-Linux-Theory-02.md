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
fcntl.h는 헤더 파일인데 오픈 시스템 콜을 쓸 때 필수로 include 해야합니다.

![추가그림2](https://ji-hun-park.github.io/assets/images/image02.png "추가그림2"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/그림28.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/그림29.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/그림30.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/그림31.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/그림32.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/그림33.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림34.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/그림35.jpg "그림12"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/그림36.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/그림37.jpg "그림14"){: .align-center}
![그림15](https://ji-hun-park.github.io/assets/images/그림38.jpg "그림15"){: .align-center}
![그림16](https://ji-hun-park.github.io/assets/images/그림39.jpg "그림16"){: .align-center}
![그림17](https://ji-hun-park.github.io/assets/images/그림40.jpg "그림17"){: .align-center}
![그림18](https://ji-hun-park.github.io/assets/images/그림41.jpg "그림18"){: .align-center}
![그림19](https://ji-hun-park.github.io/assets/images/그림42.jpg "그림19"){: .align-center}
![그림20](https://ji-hun-park.github.io/assets/images/그림43.jpg "그림20"){: .align-center}

## 작성중
![추가그림1](https://ji-hun-park.github.io/assets/images/image01.png "추가그림1"){: .align-center}

## 마무리
이상으로 Linux의 파일1편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
