---
title:  "Linux 기초 이론 03 FILE2"
excerpt: "Linux 기초 이론 세 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-02-08T20:00:00-23:59
---

# Linux Lecture Theory 03 FILE2

## Introduction
파일은 단순히 포함된 데이터로만 완전히 지정되지 않습니다.  
각 UNIX 파일에는 복잡한 다중 사용자 시스템을 관리하는 데 필요한 여러 가지 추가 기본 속성도 있습니다.  
이번 장에서 배울 것은 이러한 추가 속성과 이를 조작하는 시스템 호출(콜)입니다.

# 3.1 Files in a multi-user environment
멀티유저 환경에서 파일 시스템과 엑세스(접근)

## Users and ownerships
### Users and ownerships (1/3)
<b>소유자</b>  
유닉스는 멀티 유저 시스템, 어떤 유저가 파일을 만들면 그 파일의 오너(Owner, 소유자)가 됩니다.  
UNIX 시스템의 모든 파일은 시스템 사용자 중 한 명이 소유합니다.  
소유자는 일반적으로 파일을 만든 사용자입니다.(아닌 경우도 있음)

- 소유자의 실제 ID(identity)
    - user-id(종종 uid로 축약)
    - 특정 사용자 이름과 연관된 uid

<span style="color:#0000FF">usr/etc/passwd 안에 한 줄로 등록 됩니다.</span>  
>예제: keith:x:35:10::user/keith:bin/ksh  
>항목: login-id:password:uid:gid:user_info/home-dir/shell

- login-id – 2~8개의 영문소문자 또는 숫자로 구성된다.
- password - 13자리의 암호화된 패스워드, 저기에 저장되지 않음
- uid – 숫자로 된 유저 아이디, 0~60000
- gid – 숫자로 된 그룹 아이디, 0~60000, 대응되는 이름도 있음
- User Info – 사용자의 실제 풀 네임, 정보
- home-dir – 로그인할 디렉토리 경로(루트 밑에 홈), 시스템마다 홈 디렉은 조금씩 다름
- shell – 실행하면 $, %등 셸 프람트가 뜨는데 최초로 실행되는 셸 프로그램이 등록된다.

<span style="background-color:#ffdce0">본래에는 여기에 패스워드가 저장되었으나 보안상의 문제로 다른 곳으로 옮겼습니다.

### Users and ownerships (2/3)
각 UNIX 프로세스는 일반적으로 해당 <span style="color:red">프로세스를 시작한 사용자</span>의 uid와 연관됩니다.  
파일이 생성되면 시스템은 생성 프로세스의 uid를 참조하여 소유권을 설정합니다.  
+ 소유권 변경
    + superuser 또는 파일 소유자
    + superuser(username=root, uid=0)

![그림01](https://ji-hun-park.github.io/assets/images/그림46.jpg "그림01"){: .align-center}
로그인 아이디로 로그인하면 대응되는 uid가 있습니다.  
프로세스를 만들면 프로세스에도 uid가 대응됩니다.(프로세스를 실행시킨 사람이 누구인지)  
파일을 생성하면 생성자가 파일 오너가 됩니다.(프로세스를 기동시켜 프로세스가 파일을 만듦)  
파일을 만든 프로세스의 uid가 파일의 오너가 됩니다.

파일의 오너쉽을 바꿀 수 있습니다.(슈퍼 유저가)  
<span style="color:red">※주의</span> 단, 한 번 바꾸고 나면 상대가 다시 안 돌려준다면 오너쉽을 다시 받을 수 없습니다.

슈퍼유저(root유저)는 못 하는 게 없습니다.(username = root, uid = 0, 심지어 시스템 파일도 삭제!)

로그인하면 셸이라는 프로세스가 뜹니다.(사용자의 uid를 가짐)  
명령어를 실행하면 자식 프로세스를 만듭니다.(부모 프로세스를 상속 받아서 uid도 똑같음)

>touch – 빈 파일을 만드는 명령어

셸에서 문자를 받으면 명령어인걸 인식해서 실행 - 빈 파일의 오너도 프로세스 오너를 따라감

### Users and ownerships (3/3)
<b>그룹</b>  
각 사용자는 최소한 하나 <b>이상</b>의 `Group`에 속합니다.

```c
$ usermod -G group1,group2 user1
$ id
uid=509(user1) gid=509(group1) groups=509(group1),510(group2)
```
- /etc/group에 정의됨
- 그룹의 실제 ID(identity)
    - group-id(종종 gid로 축약)
    - 특정 그룹 이름과 연관된 gid
- gid, uid는 사용자가 시작한 프로세스에 상속됩니다.

유닉스에는 많은 유저가 있어 유저들이 모여 그룹을 형성합니다.(여러 그룹이 있을 수 있음)  
한 유저가 여러 개의 그룹에 속할 수 있습니다.(최소 1개의 그룹에 속해 있어야 한다!)
```c
$usermod –G group1, group2, user1
```
user1(자신)의 그룹을 변경한다.  

- $id = whoami 로그인한 유저의 정보를 보는 명령어
- /etc/group 파일 안에 그룹에 대한 정보가 들어있음
- gid(그룹 아이디) 그룹에 해당하는 아이디, 넘버가 있음
- gid, uid는 유저가 프로세스를 만들면 상속 받음

## Effective user-and group ids
## Permissions and file modes
### Permissions and file modes (1/3)
### Permissions and file modes (2/3)
### Permissions and file modes (3/3)
## open(2) and file permissions
### example
## The file access tests
## Extra permission for executable files
### Extra permission for executable files (1/3)
### Extra permission for executable files (2/3)
### Extra permission for executable files (3/3)
## How do you change your password?
## The file creation mask
## The umask(2) system call
### example
## The access(2) system call
### example
## The chmod(2) system call
### The chmod(2) system call (1/2)
### The chmod(2) system call (2/2)
# 3.2 File with multiple names
## File System
### File System (1/2)
### File System (2/2)
## UNIX File System
### UNIX File System (1/2)
### UNIX File System (2/2)
## i-node
## Hard link & Symbol link
### Hard link & Symbol link (1/2)
### Hard link & Symbol link (2/2)
## The link(2) system call
## The unlink(2) system call
### example
## The remove(2) system call
## The rename(2) system call
## The symlink(2) system call
## The readlink(2) system call
# 3.3 Obtaining file information: stat and fstat
## The stat(2) system call
### The stat(2) system call (1/2)
### The stat(2) system call (2/2)
### example
## 작성중
![그림00](https://ji-hun-park.github.io/assets/images/image04.png "그림00"){: .align-center}

![그림02](https://ji-hun-park.github.io/assets/images/그림47.jpg "그림02"){: .align-center}
![그림03](https://ji-hun-park.github.io/assets/images/그림48.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/그림49.jpg "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/그림50.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/그림51.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/그림52.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/그림53.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/그림54.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/그림55.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림56.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/그림57.jpg "그림12"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/그림58.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/그림59.jpg "그림14"){: .align-center}
![그림15](https://ji-hun-park.github.io/assets/images/그림60.jpg "그림15"){: .align-center}
![그림16](https://ji-hun-park.github.io/assets/images/그림61.jpg "그림16"){: .align-center}
![그림17](https://ji-hun-park.github.io/assets/images/그림62.jpg "그림17"){: .align-center}
![그림18](https://ji-hun-park.github.io/assets/images/그림63.jpg "그림18"){: .align-center}
![그림19](https://ji-hun-park.github.io/assets/images/그림64.jpg "그림19"){: .align-center}
![그림20](https://ji-hun-park.github.io/assets/images/그림65.jpg "그림20"){: .align-center}
## 마무리
이상으로 Linux 이론의 파일2편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
