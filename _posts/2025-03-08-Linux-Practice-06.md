---
title:  "Linux 기초 실습 06 파일 및 디렉터리 조작"
excerpt: "Linux 기초 실습 06 파일 및 디렉터리 조작하기"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Practice
last_modified_at: 2025-03-08T18:00:00-00:00
---

# Linux Lecture Practice 06 파일 및 디렉터리 조작

## Introduction
이번 실습에서는 파일 조작 기법 중 권한 조작하기 실습과  
링크와 디렉터리 조작 기법에 대해 알아보겠습니다.

## sys/stat.h
- 파일의 ‘상태’에 대한 정보를 얻거나 설정
  - 파일의 ‘상태’ 
    - 해당 파일이 저장된 디바이스 번호
    - 해당 파일의 inode 번호
    - 해당 파일의 Mode
    - 해당 파일 소유자의 User ID
    - 해당 파일 소유자의 Group ID
    - 해당 파일의 하드 링크 개수
    - 해당 파일의 블록 크기
    - 해당 파일의 블록 개수
    - 해당 파일을 마지막으로 액세스한 시간
    - 해당 파일을 마지막으로 편집한 시간

## sys/stat.h 실습(fstat.c)
**Mode : 파일 타입과 접근 허가(=파일 속성)**  
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG082.jpg "그림01"){: .align-center}
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG083.jpg "그림02"){: .align-center}

## 파일 접근 권한 복습
- 모든 파일과 디렉터리는 고유의 사용자와 권한이 있음
- ls –l 또는 dir –l 명령으로 확인 가능
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG084.jpg "그림03"){: .align-center}
- chmod (Change Mode)
  - 파일의 접근 권한을 변경 (파일을 소유해야 함)
- 사용법: chmod [옵션] (대상 파일)
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG085.jpg "그림04"){: .align-center}

## 하드 링크와 심볼릭 링크
- 하드 링크 (Hard Link)
  - 파일에 대한 별명(alias)
  - 파일 이름이 다르다 해도 같은 내용을 가리킴
  - 파일의 소유자만이 생성할 수 있음
- 심볼릭 링크 (Symbolic Link)
  - 유닉스에서의 ‘바로가기’
  - 누구나 생성가능
  - 원본 파일이 없으면 링크가 깨짐

![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG086.jpg "그림05"){: .align-center}

## 하드 링크 만들기 & 지우기
- `<unistd.h>` 포함
- int link(const char *old, const char *new)
  - old 파일을 new 파일에 하드 링크
  - old와 new는 같은 파일을 가리킴
- int unlink(const char *path)
  - path로 지정된 하드 링크를 제거
  - 마지막 남은 링크인 경우 사실상 파일을 삭제
- 리턴 값 : 0이면 성공, 아니면 실패

### 하드 링크 만들기 & 지우기 (mv.c 작성)
```c
#include <unistd.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
    if(argc != 3)
    {
        perror("Usage: ./mv <old> <new> \n");
        return -1;
    }
    
    if(link(argv[1], argv[2]))
    {
        printf("Link failed. : %s -> %s \n", argv[1], argv[2]);
        return -2;
    }

    /*
    if(unlink(argv[1]))
    {
        printf("Unlink failed. : %s \n", argv[1]);
        return -3;
    }
    */

    printf("File moved: %s -> %s \n", argv[1], argv[2]);
    return 0;
}
```

### link와 unlink를 이용한 파일명 바꾸기
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG087.jpg "그림06"){: .align-center}

## 디렉터리 만들기
- `<sys/types.h>`, `<sys/stat.h>`
- int mkdir(const char *pathname, mode_t mode)
  - pathname : 생성할 디렉터리 경로
  - mode : 생성할 디렉터리의 권한
  - 리턴 값 : 0이면 성공, 아니면 실패

### 디렉터리 만들기 (md.c 작성)
```c
#include <sys/stat.h>
#include <sys/types.h>

int main(int argc, char* argv[])
{
    int i;
    for(i = 1; i < argc; ++i)
      mkdir(argv[i], 0777);
    return 0;
}
```

![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG088.jpg "그림07"){: .align-center}

## 시스템 호출로서의 chmod
- chmod(파일명, 권한)
  - `sys/stat.h` 헤더에 포함
  - 파일명 : 권한을 바꿀 대상 파일
  - 권한 : 덮어쓸 권한을 설정

## chmod vs. umask (복습)
- umask(마스크)
  - 파일이나 디렉터리를 만들 때 권한을 마스킹
  - 마스크 역시 8진수 형식으로 설정
  - 최종 권한 : 0777 & ~마스크
- 실제 결과는 open()을 해야 확인 가능

## umask 실습 (md_umask.c 편집)
```c
#include <sys/stat.h>
#include <sys/types.h>

int main(int argc, char* argv[])
{
    int i;

    umask(0000); // mask: 000
    for(i = 1; i < argc; ++i)
      mkdir(argv[i], 0777);
    return 0;
}
```
```
  777 & ~000
= 777 & 777
= 777
```

![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG089.jpg "그림08"){: .align-center}

## 디렉터리 지우기
- `<unistd.h>` 포함
- int rmdir(const char *pathname)
  - pathname : 삭제할 디렉터리 경로
    - 디렉터리는 반드시 비어 있어야 함
  - 리턴 값 : 0이면 성공, 아니면 실패

### 디렉터리 지우기 (rm.c 작성)
```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char* argv[])
{
    int i, res;
    dup2(1, 2); // stderr에 stdout 덮어씀
    for(i = 1; i < argc; ++i)
    {
        res = rmdir(argv[i]);
        if(res)
          printf("rmdir for %s failed. \n", argv[i]);
    }

    return 0;
}
```

![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG090.jpg "그림09"){: .align-center}

## 디렉터리 위치 변경
- `<unistd.h>` 포함
- int chdir(const char *pathname)
  - pathname : 이동할 디렉터리 위치
  - 리턴 값 : 0이면 성공, 아니면 실패
  - 디렉터리 위치는 현재 프로세스에 한정
- int fchdir(int fd)
  - fd : 이동할 디렉터리의 파일 서술자
  - 나머지는 chdir()과 동일

## 디렉터리 위치 확인
- `<unistd.h>` 포함
- char* getcwd(char *name, size_t size)
  - name : 현재 위치를 저장할 포인터이자 리턴 값
  - size : name의 크기 (충분히 커야 함)

## 디렉터리 위치 변경 및 확인 (cd.c 작성)
```c
#include <unistd.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
    char name[256];
    int i, res;

    printf("Current Dir.: %s \n", getcwd(name, 256));
    for(i = 1; i < argc; ++i)
    {
        res = chdir(argv[i]);
        if(res)
          printf("Chdir for %s failed. \n", argv[i]);
        else
          printf("Current Dir.: %s \n", getcwd(name, 256));
    }
    return 0;
}
```

![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG091.jpg "그림10"){: .align-center}

## 디렉터리 엔트리?
- Unix에서는 모든 것을 파일로 관리함
  - 일반 파일, 블록 파일, 디렉터리 파일, 장치 파일…
- 디렉터리는 특수한 형태의 파일
- 디렉터리 엔트리는 디렉터리를 표현하는 데에 쓰이는 자료구조
- 디렉터리에 속한 파일들은 디렉터리 엔트리 구조체로 관리

## 디렉터리 엔트리
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG092.jpg "그림11"){: .align-center}

## 디렉터리 엔트리 구조
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG093.jpg "그림12"){: .align-center}

## 디렉터리 열기
- `<dirent.h>` 포함
- DIR *opendir(const char *pathname)
  - pathname : 디렉터리의 경로
  - 리턴 값 : NULL이면 실패, 아니면 성공
- int closedir(DIR *dirp)
  - 열려있던 디렉터리를 닫음

## 디렉터리 읽기
- `<dirent.h>` 포함
- struct dirent *readdir(DIR *dirp)
  - dirp : 열려 있는 디렉터리 포인터
  - 리턴 값 : 디렉터리 엔트리 (NULL이면 실패)
  - 디렉터리의 엔트리를 하나 읽어옴
  - 읽은 뒤에는 다음 엔트리로 넘김
- void rewinddir(DIR *dirp)
  - dirp의 포인터를 처음으로 되돌림

## 디렉터리 열기와 읽기 (list.c 작성)
```c
#include <dirent.h>
#include <stdio.h>

int main(int argc, char* argv[])
{
    int res, depth = 0;
    DIR* dirp;
    struct dirent* entry;

    if(argc != 2)
    {
        perror("Usage: list <path> \n");
        return -1;
    }

    dirp = opendir(argv[1]);
    if(dirp == NULL)
    {
        perror("Directory cannot be opened. \n");
        return -2;
    }
    while((entry = readdir(dirp)) != NULL)
      printf("%10ld : %s \n", entry->d_ino, entry->d_name);
    closedir(dirp);
    return 0;
}
```

![그림13](https://ji-hun-park.github.io/assets/images/LNXIMG094.jpg "그림13"){: .align-center}
현재 디렉터리에 대한 inode 번호와 파일명이 모두 표시  
(현재와 상위 디렉터리도 표시)

## Background에서 작업 수행하기
```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    while(1)
    {
        printf("Knock! \n\n");
        sleep(5);
    }

    return 0;
}
```
![그림14](https://ji-hun-park.github.io/assets/images/LNXIMG095.jpg "그림14"){: .align-center}
- Ctrl + Z
  - 현재 foreground에서 실행 중인 작업을 중지시킴
![그림15](https://ji-hun-park.github.io/assets/images/LNXIMG096.jpg "그림15"){: .align-center}
- &
  - 명령어 뒤에 &를 붙이면 background에서 작업을 수행함
![그림16](https://ji-hun-park.github.io/assets/images/LNXIMG097.jpg "그림16"){: .align-center}

## 명령어 – jobs
- jobs 
  - Background에서 실행되는 작업목록 (Job ID, 상태, 명령어)을 보여주는 명령어
![그림17](https://ji-hun-park.github.io/assets/images/LNXIMG098.jpg "그림17"){: .align-center}
  - Job ID는 PID와는 별도로 부여되는 번호
  - 작업목록은 현재 shell session에 딸린 것이며, 다른 session 과는 독립적이다.

![그림18](https://ji-hun-park.github.io/assets/images/LNXIMG099.jpg "그림18"){: .align-center}

## 명령어 - bg
- bg %(ID)
  - Stopped 된 작업을 백그라운드에서 수행시킴
  - 사용법 : bg %JobID

![그림19](https://ji-hun-park.github.io/assets/images/LNXIMG100.jpg "그림19"){: .align-center}

## 명령어 - fg
- fg %(ID)
  - Background에서 수행중인 작업을 Foreground로 옮김
  - 사용법 : fg %JobID

![그림20](https://ji-hun-park.github.io/assets/images/LNXIMG101.jpg "그림20"){: .align-center}

## 명령어 – kill
- kill 
  - 작업을 끝내거나, 프로세스를 종료시키는데 사용
  - 사용법
    - kill %JobID : 수행중인 Job을 종료시킴
![그림21](https://ji-hun-park.github.io/assets/images/LNXIMG102.jpg "그림21"){: .align-center}
    - kill PID : 해당 프로세스 ID를 가진 프로세스를 종료

## 명령어 – kill -9
- kill -9
![그림22](https://ji-hun-park.github.io/assets/images/LNXIMG103.jpg "그림22"){: .align-center}
  - 강제 종료 (권장 X)

![그림23](https://ji-hun-park.github.io/assets/images/LNXIMG104.jpg "그림23"){: .align-center}

## 마무리
이상으로 이번 실습을 마치겠습니다.  
열심히 따라와 주셔서 감사합니다!
