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
- Real(실제) user-id (ruid)
    - 프로세스를 시작한 사용자의 uid.
- Effective(유효) user-id (euid)
    - 특정 작업을 수행하기 위한 프로세스의 권한을 평가하는 데 사용.
- 대부분의 경우 유효 사용자 ID와 실제 사용자 ID는 일치합니다.
- 유효 사용자 ID와 그룹 ID는 파일 액세스 권한을 결정합니다.

![그림02](https://ji-hun-park.github.io/assets/images/그림47.jpg "그림02"){: .align-center}
uid는 2종류(리얼,이펙티브) - ruid, euid

리얼 유저 아이디는 프로세스를 실행시킨 유저 아이디(프로세스를 누가 실행시켰는지)입니다.  
이펙티브 유저 아이디는 대개 리얼 유저 아이디랑 같은데, 다른 경우가 있습니다.  
프로세스가 파일을 만들면 파일의 오너는 euid가 됩니다.(파일의 대한 권한은 전부 euid)  
이펙티브 유저아이디(그룹아이디)가 파일의 엑세스 퍼미션(접근 권한)을 결정합니다.(그룹도 이펙티브가 중요!)

쉘이 프로세스를 만들면 ruid,euid가 같아집니다.  
쉘에서 패스워드(유저의 패스워드를 바꾸는 파일)라는 파일(/etc/shadow에 있고, 파일 오너는 루트, 루트만이 엑세스 가능)을 실행하면 euid가 0으로 바뀝니다(ruid는 그대로), 이 경우 두 아이디가 서로 달라집니다!

## Permissions and file modes
### Permissions and file modes (1/3)
<b>Ownership(오너쉽, 소유권)</b>  
모든 파일은 오너쉽이 존재하며, 파일과 관련된 권한을 선택할 수 있습니다.

<b>Permission(퍼미션, 권한)</b>  
- 다양한 사용자가 파일에 액세스할 수 있는 방법을 결정합니다.
- 슈퍼유저는 읽기, 쓰기 또는 실행 여부에 관계없이 모든 파일을 조작할 수 있습니다.
![그림03](https://ji-hun-park.github.io/assets/images/그림48.jpg "그림03"){: .align-center}
퍼미션은 유저 타입이 유저,그룹,아더스로 나뉘고 파일 퍼미션이 리드,라이트,엑스큐트로 나뉩니다.  
퍼미션은 3비트이기 때문에 2진수로 바꿀 수 있습니다.(1이면 퍼미션 있음)  
슈퍼 유저는 퍼미션에 관계없이 모든 것을 다 할 수 있습니다!

### Permissions and file modes (2/3)
![그림04](https://ji-hun-park.github.io/assets/images/그림49.jpg "그림04"){: .align-center}
```c
761 = 111 110 001 = rwxrw---x
```
vi(에디터)의 퍼미션은 rwxr-xr-x입니다.  
오너는 root, 그룹도 루트(시스템 파일)입니다.  
라이트 퍼미션이 없으면 수정도 삭제도 불가능합니다.

### Permissions and file modes (3/3)
![그림05](https://ji-hun-park.github.io/assets/images/그림50.jpg "그림05"){: .align-center}
```c
S_IRUSER | S_IRGRP | S_IROTH = 0444 = r--r--r--
S_IRUSR | S_IWUSR | S_IXUSR | S_IRGRP | S_IXGRP | S_IXOTH | S_IXOTH
  = 0755 = rwx-r-xr-x
```

## open(2) and file permissions
<b>open을 사용하여 기존 파일을 여는 경우</b>  
- 시스템은 프로세스에서 요청한 액세스 모드가 파일의 권한을 확인하여 허용되는지 확인합니다.
- 프로세스에 요청된 액세스 권한이 없는 경우 open은 -1을 반환합니다(errno=EACCESS)
- <span style="color:red">파일을 열 때 커널은 유효(effective) 사용자 및 그룹 ID를 기반으로 액세스 테스트를 수행합니다.</span>

```c
filedesc = open(pathname, O_RDONLY|O_CREAT|O_TRUNC, 0600);
/* the file in question will be truncated if it exists */


filedesc = open(pathname, O_RDONLY|O_CREAT|O_EXCL, 0600);
/* if the file exists, return -1 (errno = EEXIST)*/
```
+ 오픈 시스템 콜은 여러 가지 플래그와 함께 사용됨
+ 파일이 없으면 실패하고, 파일이 있다 하더라도 퍼미션을 체크
+ 프로세스가 요청한 엑세스모드(리드온리,라이트온리,리드라이트 등등)에 대해 퍼미션이 있는지를 체크한다, 퍼미션이 없으면 오픈 실패
+ 요청한 퍼미션이 없으면 –1, 엑세스 에러
+ 이 때 이펙티브 유저 아이디와 이펙티브 그룹 아이디로 체크함
+ 익스클루시브 오아 사용시 배타적으로 파일이 없으면 만들어서 여는 것만 하고 이미 있으면 실패
+ 에러 넘버는 EEXIST 세팅

### example
```c
-r--r--rw- usr1 grp1 file1
-r--rw---- usr2 grp1 file2
-------r-- usr1 grp1 file3
-rw-rw-rw- usr1 grp2 file4
--w-rw---- usr2 grp2 file5
						
-rwxrwxrwx usr1 grp1 a.out
```
![그림06](https://ji-hun-park.github.io/assets/images/그림51.jpg "그림06"){: .align-center}
```c
fd1 = open(“file1”, O_RDONLY);

fd2 = open(“file2”, O_RDONLY);

fd3 = open(“file3”, O_RDONLY);

fd4 = open(“file4”, O_RDONLY);

fd4 = open(“file5”, O_RDONLY);
```
- 5개의 파일이 있다.
    - 파일 타입, 유저 퍼미션, 그룹 퍼미션, 아더 퍼미션, uid, gid, 파일명
- 유저 쉘에서 a.out으로 프로세스 생성하면
    - 셸의 리얼, 이펙티브, 유저, 그룹 아이디를 똑같이 상속 받는다.
- 5개를 오픈할 때 이펙티브 id를 본다.(리얼과 이펙티브가 같은 상황)
    - fd1은 유저가 같아 성공
    - fd2는 그룹이 같아 성공
    - fd3는 유저가 같아도 권한이 없어서 실패
    - fd4는 유저가 같고, 리드 권한이 있어서 성공
    - fd5는 유저, 그룹이 다르고, 아더스는 권한이 없어서 실패

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
