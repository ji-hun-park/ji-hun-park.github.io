---
title:  "Linux 기초 실습 05 파일 조작"
excerpt: "Linux 기초 실습 05 파일 조작하기"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Practice
last_modified_at: 2025-03-05T18:00:00-00:00
---

# Linux Lecture Practice 05 파일 조작

## Introduction
이번 실습에서는 System Call을 이용한 실습과  
파일 생성과 조작 기법에 대해 알아보겠습니다.

## System Call (시스템 호출)
- ‘프로그램’과 ‘커널’ 사이를 연결하는 API
  - 프로그램 만으로는 불가능한 기능을, 커널의 기능을 빌려서 수행
- Windows에도 ‘시스템 호출’이 있다
  - WIN16 API, WIN32 API, WIN64 API
  - 프로그램의 하위 호환성을 제공
    - Win 2.0의 프로그램이 Win 7에서도 수행

## Unix 시스템의 구조
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG073.jpg "그림01"){: .align-center}

## unistd.h & fcntl.h
```
 unistd.h
```
- **Uni**x **St**an**d**ard **H**eader
- Unix 시스템 호출 전반을 담당하는 헤더 파일
  - 사용자 ID 획득
  - 그룹 ID 획득
  - 파일 및 장치 읽기/쓰기
  - 프로세스 복제
  - 파이프
  - 그 외 다수의 시스템 호출

```
 fcntl.h
```
- **F**ile **C**o**nt**ro**l** **H**eader
- Unix 시스템 중 파일을 담당하는 헤더 파일
  - 파일 생성
  - 파일 속성 변경

## 복습 - 사용자 및 그룹 확인
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG074.jpg "그림02"){: .align-center}

## 사용자 및 그룹 확인
- getuid(): 사용자의 ‘실제’ ID 출력
- geteuid(): 사용자의 ‘유효’ ID 출력
- getgid(): 사용자의 ‘실제’ 그룹 ID 출력
- getegid(): 사용자의 ‘유효’ 그룹 ID 출력

### uid.c 작성
```c
#include <stdio.h>
#include <unistd.h>
int main(){
    printf("UID : %d, EUID : %d\n",getuid(), geteuid());
    printf("GID : %d, EGID : %d\n",getgid(), getegid());
    return 0;
}
```
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG076.jpg "그림04"){: .align-center}

## ‘실제’ ID와 ‘유효’ ID?
- 실제 ID : 프로그램의 ‘실행을 명령’하는 ID
- 유효 ID : 프로그램을 ‘실제로 수행’하는 ID
- ‘chmod +s’로 유효 ID 우회가 가능

![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG077.jpg "그림05"){: .align-center}
>su : substitute user(사용자 ID를 임시로 다른 ID로 변경)

## 파일 열기 및 생성
- C언어 : fopen() (stdio.h)
- Unix 호출 : open() (fcntl.h)
- 사용법 : open(파일명, 모드, 권한)
  - 파일명 : 읽거나 쓸 파일 이름
  - 모드 : 읽거나 쓸 때의 모드 지정
  - 권한 : 파일을 새로 만들 때에만 권한 지정
    - 권한 생략 시 754로 지정(`rwxr-xr--`)

- C언어 : **FILE*** fp = fopen(“input.txt”, “w+”);
  - 반환되는 값은 포인터로 표현된 파일 포인터
- Unix : **int** fd = open(“input.txt”, O_RDWR);
  - 반환되는 값은 정수로 표현된 파일 서술자
- 파일 서술자(File Descriptor)?
  - 파일에 접근하기 위한 추상적인 정수 키
  - 키의 관리는 커널에서 수행

## 미리 정의된 파일 서술자
- 0 : stdin (표준 입력, 파일로부터 읽기만 가능)
- 1 : stdout (표준 일반 출력, 파일에 쓰기만 가능)
- 2 : stderr (표준 오류 출력, 파일에 쓰기만 가능)
- 파일 서술자 추가 시, 할당되지 않은 서술자 중 가장 작은 번호로 자동 할당

## 파일 열기 옵션
- O_RDONLY : 읽기 전용으로 열기
- O_WRONLY : 쓰기 전용으로 열기
- O_RDWR : 읽기/쓰기 겸용으로 열기
- O_APPEND : 파일의 끝에서부터 추가
  - 이 옵션이 없으면 파일의 처음부터 시작
- O_CREAT : 파일이 없으면 새로 생성
  - 이 옵션이 없으면 파일 없을 때 오류 발생
- O_TRUNC : 파일이 이미 있으면 내용을 삭제
  - 이 옵션이 없으면 내용을 삭제하지 않고 덮어 씀
- O_EXCL : 파일이 이미 있으면 오류 발생

## 파일 생성
- int fd = **creat** (파일명, 권한);
  - 파일을 쓰기 전용으로 새로 생성
    - O_WRONLY \| O_CREAT \| O_TRUNC와 동일
  - 파일이 있으면 초기화 후 덮어씀
  - 권한은 open()의 권한과 동일

## 파일 닫기
- close(fd);
  - 열려 있는 파일을 닫음 (파일 서술자 경유)

## 파일 서술자 복제
- int fd2 = **dup**(fd1);
  - fd1의 파일을 fd2로 복제 (fd1: a.txt → fd2: a.txt)
  - fd1과 fd2는 같은 파일을 가리킴
  - fd2의 키는 자동으로 지정
- **dup2**(fd1, fd2);
  - int fd2 = dup(fd1) 와 같은 기능을 수행
  - fd2가 이미 열려있다면 파일을 닫은 후 복제
  - fd2의 키를 수동으로 지정할 수 있음

## 파일 열기, 닫기, 복제 실습(dup.c 작성)
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG078.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG079.jpg "그림07"){: .align-center}
>dup2() 이후 **test.txt 파일에 출력**되었다.  
(표준 입출력 자체에도 영향을 끼칠 수 있다.)

## 파일에서 읽기
- int result = **read**(fd, buffer, bytes)
  - fd : 읽을 파일의 서술자
  - buffer : 결과를 저장할 버퍼(포인터 형식)
  - bytes : 파일에서 읽을 바이트 수
  - result : 실제로 읽은 바이트 수
    - \> 0 : 버퍼에 실제 내용이 들어감
    - = 0 : 파일의 끝(EOF)
    - < 0 : 오류 발생

## 파일로 쓰기
- int result = **write**(fd, buffer, bytes)
  - fd : 쓸 파일의 서술자
  - buffer : 내용이 저장되어 있는 버퍼(포인터 형식)
  - bytes : 파일에서 쓸 바이트 수
  - result : 실제로 쓴 바이트 수
    - \> 0 : 파일에 실제 내용이 쓰였음
    - = 0 : 쓴 내용 없음
    - < 0 : 오류 발생

## 파일 읽기, 쓰기 실습 (read.c)
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG080.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG081.jpg "그림09"){: .align-center}

## Command line 매개변수 사용법
```c
#include <stdio.h>
int main(int argc, char* argv[]){

}
/* == */
int main(int argc, char** argv){

}
```
- argc : 매개변수의 개수 (파일 실행 명령어 포함)
- argv : 각각의 매개변수

```c
#include <stdio.h>

int main(int argc, char* argv[]){
	int count;
	printf(“argc : %d\n”, argc);
	printf(“------arguments list------\n”);
	for(count=0; count < argc ; count++){
		printf(“argv[%d] : %s\n”, count, argv[count]);
	}
	return 0 ;
}
```

## 파일 접근 Primitive
- lseek()
  - 특정 파일의 파일포인터 위치를 변경해주는 함수
  - off_t  lseek(filedes, offset, flag)
  - 매개변수
    - filedes :  파일 descriptor
    - offset : 시작 위치로부터의 추가 offset (음수 가능)
    - flag : offset 시작 위치
      - SEEK_SET		파일의 처음부터 시작
      - SEEK_CUR		파일 포인터의 현재 위치부터 시작
      - SEEK_END		파일의 끝부터 시작
  - 반환값
    - 성공 : offset
    - 실패 : 음수

# 예제 프로그램 2.1.11 호텔
## Hotel
>호텔에 투숙하고 있는 사람의 이름을 기록하고 있는 파일(listfile)을 관리 

- 투숙자 관리 파일 
  - 각 줄의 길이 4개 문자 
    - 처음 세개 문자 : 이름 
    - 끝 한 개 문자 : `‘\0’` 

- 방 번호에 투숙하고 있는 사람의 이름을 출력하는 getoccupier를 반복적으로 호출하여 모든 방에 있는 사람의 이름을 출력 

- hotel.h, hotel.c, getoccupier.h, getoccupier.c 로 구성 
- Makefile로 컴파일

### listfile
```
111
222
333
444
555
666
777
888
999
000
```
>이 숫자들이 투숙객 이름이라 생각하고  
cat > listfile 명령을 작성하자.

### makefile
```
hotel :  hotel.o getoccupier.o

hotel.o : hotel.h hotel.c

getoccupier.o : hotel.h getoccupier.h getoccupier.c

clean : 
	rm *.o
```

### hotel.h
```c
#ifndef LINELENGTH
#define LINELENGTH 4
#endif

// 중복 선언을 막기 위해 static으로 선언
// 좋은 방식은 아님
static int listfiledes = -1;
static char listfile[] = "listfile";

```

### getoccupier.h
```c
char* getoccupier(int roomno);
```

### hotel.c
```c
#include "hotel.h"
#include "getoccupier.h"
#include <stdio.h>

#ifndef NROOMS
#define NROOMS 10
#endif

int main()
{
    char* name;
    int i;

    printf("Read occupiers from file : %s \n", listfile);

    for(i = 1; i <= NROOMS; i++){
        if(name = getoccupier(i))
            printf("Room %d, %s \n", i, name);
        else
            fprintf(stderr, "Error on calling 'getoccupier'\n");
    }
    return 0;
}
```

### getoccupier.c
```c
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include "hotel.h"
#include "getoccupier.h"

// 문자열 return을 위해 전역변수로 설정
char buffer[LINELENGTH];

char *getoccupier(int roomno){
    off_t offset;
    ssize_t nread;

    // 파일 읽기 실패
    if(listfiledes == -1 && (listfiledes = open(listfile, O_RDONLY)) == -1){
        fprintf(stderr, "Error on opening list file \n");
        return NULL;
    }
    // 방 번호로부터 offset계산
    offset = (roomno - 1) * LINELENGTH;

    // offset으로 파일포인터 이동
    if(lseek(listfiledes, offset, SEEK_SET) == -1){
        fprintf(stderr, "Error on setting offset \n");
        return NULL;
    }

    // offset부터 파일 읽기
    if((nread = read(listfiledes, buffer, LINELENGTH)) < 0){
        fprintf(stderr, "Error on reading offset \n");
        return NULL;
    }

    // 각 행 마지막에 있는 개행문자를 null 문자로 대체하여 하나의 스트링 생성
    buffer[LINELENGTH - 1] = '\0';

    return buffer;
}
```

## 마무리
이상으로 이번 실습을 마치겠습니다.  
열심히 따라와 주셔서 감사합니다!
