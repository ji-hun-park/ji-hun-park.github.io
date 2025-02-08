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
<b>커널이 파일 엑세스 퍼미션을 테스트하는 과정 정리(이전 조건이 맞으면 끝, 아니면 다음 단계로)</b>  
1. 만약 프로세스의 이펙티브 유저 아이디가 <b>0</b>이면 슈퍼 유저이므로 무조건 허용
2. 만약 프로세스의 이펙티브 유저 아이디가 파일의 오너 아이디랑 같으면서, 엑세스 퍼미션이 있다면 허용, 없으면 퍼미션이 디나이되어 실패하고 끝
3. 만약 프로세스의 이펙티브 그룹 아이디가 같으면서, 그룹 엑세스 퍼미션이 있으면 허용, 없으면 퍼미션 디나이하고 끝
4. 만약 아더스의 퍼미션이 있으면 허용, 없으면 퍼미션 디나이하고 끝

## Extra permission for executable files
### Extra permission for executable files (1/3)
<b>일반적으로 파일에 실행 가능한 프로그램이 들어 있는 경우에만 해당됩니다.</b>
![그림07](https://ji-hun-park.github.io/assets/images/그림52.jpg "그림07"){: .align-center}
- S_ISUID 권한(permission)이 설정된 경우 실행 파일이 시작되면,
    - 시스템은 프로세스를 시작한 사용자의 사용자 ID가 아닌 파일 소유자(file owner)로부터 가져온 유효(effective) 사용자 ID를 결과 프로세스(resulting process)에 제공합니다.

사실 퍼미션은 기존 3가지(r,w,x) 말고 3가지 더 있습니다.  
스티키 비트(sticky bit)는 별로 안 씁니다.

- 옥탈(Octal, 8진수)로 3번째가 유저 퍼미션, 4번째가 그룹 퍼미션, 5번째가 아더스 퍼미션
- 2번째 자리는 사실 3비트
    - 3비트 중에서 첫 번째 비트가 1이면 셋 유저 아이디 온 엑스큐션 퍼미션(Set user-id on execution)
    - 2번째 비트가 1이면 그룹 아이디(Set group-id on execution)
    - 3번째 비트가 1이면 스티키 비트(sticky bit)
- 셋 유저 아이디 온 엑스큐션(Set user-id on execution) 퍼미션이 셋(Set)되면 <b>실행파일</b>이 `실행`할 때만 의미가 있는 퍼미션
- 비트(S_ISUID)가 세트되어 있는 실행파일을 시작할 때 시스템(커널)에서 프로세스가 만들어지고 uid의 리얼,이펙티브가 세팅
- 셸의 리얼 유저 아이디(로그인한 자)를 uid로 설정
- 이와 같은 euid를 세팅하는데 저 비트(S_ISUID)가 세팅되어 있으면
    - 셸의 유저 아이디로 이펙티브 유저 아이디를 세팅하지 않고
        - 파일의 오너의 유저 아이디를 이펙티브 유저 아이디로 취한다.
- 이 경우 리얼 유저 아이디랑 이펙티브 유저 아이디가 <b>달라진다.</b>

### Extra permission for executable files (2/3)
![그림08](https://ji-hun-park.github.io/assets/images/그림53.jpg "그림08"){: .align-center}
\$ id로 내가 누구냐? 물어보면 답변으로 user1(아이디 100), group1(아이디 500)을 알려줍니다.  
a.out이란 파일의 오너는 user2(타인), 해당 파일을 실행합니다.  
셸의 여러 아이디는 내가 됩니다.  
실행하면 a.out에서 프로세스 아이디는 내가 됩니다.

다시 한 번 나를 확인하고, 체인지 모드(chmod) 실행(퍼미션을 바꿔줌)합니다.  
퍼미션의 9비트 위에 3비트가 더 있습니다.(셋 유저,셋 그룹,스티키 비트 / s,g,t)  
셋 유저 아이디 플래그를 1로 세팅합니다(u+s).  
권한 X가 S로 바뀝니다.(X가 있어야 S가 의미 있음)

다시 실행해보면 리얼 유아이디(ruid)는 그대로인데 이펙티브 유저 아이디(euid)는 파일의 오너로 바뀌었습니다.  
이런 경우가 리얼과 이펙티브 유저(그룹) 아이디가 <b>다른</b> 경우입니다!

실행 파일은 퍼미션(permission, 권한)을 이펙티브 유저 아이디로 구분, 해당 파일 오너의 권한으로 실행합니다.

### Extra permission for executable files (3/3)
<b>sticky bit ( save-text-image )</b>  
스티키 비트(세이브 텍스트, 이미지)는 디렉토리를 엑세스할 때 의미가 있으나, 예전의 의미로, 지금은 사용하지 않습니다.(참고만 할 것)

- S_ISVTX 비트는 실행 파일에 설정할 수 있었습니다.
- 이전 시스템에서는 파일에 save-text-image 비트가 설정된 경우 실행되고, 해당 program-text 부분은 시스템이 중단될 때까지 시스템의 스왑 영역에 남아 있었습니다.
- 현대 UNIX 시스템에서는 이 비트가 이제 중복됩니다.
- 현대 시스템은 디렉토리에 대해 S_ISVTX 비트를 정의합니다.

## How do you change your password?
![그림09](https://ji-hun-park.github.io/assets/images/그림54.jpg "그림09"){: .align-center}
<b>패스워드를 변경하는 경우</b>  
<b>/etc/shadow를 직접 변경할 수 없습니다.</b>  
<b>하지만 usr/bin/passwd는 /etc/shadow를 변경할 수 있습니다.</b>

오너는 root, 셋 유저가 1로 세팅되어 있습니다.  
`bin`의 `passwd`를 실행해서 비밀번호를 바꾸면 etc의 shadow가 업데이트 됩니다.  
shadow의 오너는 root이고, 오너만이 read 가능합니다.

나는 누구냐?(whoami) - 유저 아이디 100(user1)이라고 알려줍니다.  
패스워드를 바꾸라는 메시지 출력 후 프로그램이 실행 됩니다.

나의 셸 아이디는 원래 이러했으나(100, user1), `passwd`를 실행하면 나머지는 그대로 있고 `euid`만 <b>0(root)</b>가 됩니다!  
이는 권한 s가 세팅되어 있기에 이렇게 바뀌게 되는 겁니다.

그러면 이제 root의 권한으로 패스워드를 바꿀 수 있습니다(shadow에서 업데이트함).

![그림10](https://ji-hun-park.github.io/assets/images/그림55.jpg "그림10"){: .align-center}

## The file creation mask
- 각 프로세스와 관련된 값을 파일 생성 마스크라고 함함
- 파일 생성 마스크(file creation mask, 보호(safeguarding))
    - 파일이 생성될 때마다 특정 권한 비트를 자동으로 끔
    - 지정된 권한이 실수로 켜지는 것을 방지함
- 각 프로세스는 디폴트 파일 생성마스크 값을 가짐
- 이것은 생성되는 파일에 부여되지 말아야 할 허가 권한들의 비트 마스크임
- 사용자가 open이나 creat 시스템 콜을 사용할 때 mode를 지정하면 커널은 디폴트 마스크에 지정된 비트들을 소거함

![그림00](https://ji-hun-park.github.io/assets/images/image04.png "그림00"){: .align-center}
- mode에 &(비트와이즈앤드) 연산 수행, 위에선 mask에 ~(비트와이즈니게이션)함
- 프로세스가 생성되면 프로세스의 액티베이트(activate) 값처럼 붙어 다님
- 세이프가드(안전장치) 역할
- 파일을 만들면 퍼미션을 세팅하는데 특정 퍼미션은 중요할 수 있음
- 특별한 퍼미션 비트를 On하지 못하도록 자동으로 turn off 시킴
- 우연히 turn on 되는 것을 방지해 주는 역할
- 마스크 설정은 자동으로 Off하고 싶은 위치만 1로 설정(프로세스가 실행되기 전에 자동으로 설정되어져 있음)
- 마스크를 안 붙여도 자동으로 마스크 붙인 채로 실행 되어짐
- 그룹이나 아더스의 라이트 퍼미션을 자동으로 Off함
    - 해당 권한을 주고 싶으면 마스크에서 해당 값을 지움

## The umask(2) system call
```c
#include <sys/stat.h>

mode_t umask(mode_t cmask);  /* cmask는 new mask, return은 old mask */

//mode_t oldmask; mode_t는 int 정도, 이전 마스크 값 저장

//Returns: previous file mode creation mask
```
>umask 함수는 프로세스에 대한 파일 모드 생성 마스크를 설정합니다.

```c
umask(1) 
	$ umask
	$ umask –S //값을 보여줌
	$ umask 22 //마스크 값을 22로 바꿔줌
```
```c
mode_t oldmask;		/* 이전 마스크 값을 저장하기 위한 */
.
.
.
oldmask = umask(022);	/*  파일 소유자 이외의 사용자에게 쓰기 허가를 막는다 */
```

### example
```c
#include <fcntl.h>
#include <sys/stat.h>

int specialcreat (const char *pathname, mode_t mode){
  mode_t oldu;
  int filedes:

  /* 파일 생성 마스크를 0으로 설정 */
  if ( (oldu = umask(0)) == ―1){
 	perror ("saving old mask");
 	return (-1);
  }

 /* 파일을 생성한다 */
 if((filedes=open(pathname, O_WRONLY｜O_CREAT｜O_EXCL, mode)) == -1)
	 perror ("opening file");

 /* 비록 개방에 실패하더라도, 과거의 파일 모드를 복원한다. */
 if (umask (oldu) == -1) 
	 perror ("restoring old mask");

 /* 파일 기술자를 복귀한다. */
 return filedes;
}
```
- 마스크를 0으로 바꾸면 그룹, 아더스의 라이트 설정 가능
- 올드 마스크와 유마스크 값을 서로 바꿈
- 모드를 777로 하면 설정이 된다.
- 이후 올드 마스크와 뉴마스크를 다시 서로 바꾼다.

## The access(2) system call
```c
#include <unistd.h>

int access(const char *pathname, int amode);

//Returns: 0 if OK,-1 on error
```
- 실제(real) 사용자 및 그룹 ID를 기반으로 경로 이름(pathname)의 액세스(access) 권한(permissions)을 확인합니다.
* Arguments(인자들)
    - amode
        + R_OK 읽기 권한에 대한 테스트.
        + W_OK 쓰기 권한에 대한 테스트.
        + X_OK 실행 권한에 대한 테스트.
        + F_OK 파일 존재 여부에 대한 테스트

![그림11](https://ji-hun-park.github.io/assets/images/그림56.jpg "그림11"){: .align-center}
`<unistd.h>` 반드시 include  
엑세스를 실행하는 프로세스에서 Real 유저(그룹) ID 기반으로 저 파일(pathname)에 대해 주어진 퍼미션(amode)이 있는지 없는지 물어봅니다.  
R_OK 리드 퍼미션 있냐  
W_OK 라이트 퍼미션 있냐  
X_OK 엑스큐트 퍼미션 있냐  
F_OK 파일 엑시스턴스(패스 네임 파일이 있냐)

### example
```c
/*access의 사용 예 */

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

main()
{
  char *filename = "afile";

  if (access (filename, R_OK) == -1)
  {
 	fprintf (stderr, "User cannot read file %s\n", filename);
	exit (1);
  }
 
 printf ("%s readable, proceeding\n", filename);

  /* 프로그램의 나머지 부분... */
}
```
이 프로그램을 실행하는 프로세스의 리얼 유저 아이디를 기준으로 `afile`이란 파일의 리드 퍼미션이 있는가를 체크합니다.

## The chmod(2) system call
```c
#include <unistd.h>

int chmod(const char *pathname, mode_t newmode);

//Returns: 0 if OK,-1 on error
```
기존 파일의 권한을 변경합니다.  
파일 소유자 또는 슈퍼유저만 가능합니다.
```c
if( chmod(pathname, 0644) == -1 )
    perror(“call to chmod failed”);
```
퍼미션을 바꾸는 시스템 콜입니다.($ chmod g+y test – 이거는 커멘드(1))

`pathname`에 파일명 넣고, `newmode`에 문자는 안 들어가고 숫자만 들어갑니다.(옥탈(8진수) 넘버, 0으로 시작)  
`newmode` 퍼미션으로 `pathname`의 퍼미션을 변경합니다.  
권한은 이 프로세스를 실행하는 이펙티브(유효) 유저 아이디로 체크합니다.(파일의 오너와 같아야 한다)  
슈퍼 유저면 무조건 바꿀 수 있습니다.

## The chown(2) system call
```c
#include <unistd.h>

int chown(const char *pathname, uid_t owner_id, gid_t group_id);


//Returns: 0 if OK,-1 on error
```
- 파일의 사용자 ID와 파일의 그룹 ID를 변경할 수 있도록 합니다.
- Arguments(인자들)
    - owner_id : 새 소유자
    - group_id : 새 그룹
- 오류 **EPERM**은 파일의 소유권을 변경하려는 모든 불법적인(illegal) 시도에서 항상 return됩니다.
- 해당 파일의 소유권이 변경되면 해당 파일에 대한 set-user-id 및 set-group-id 권한(permission)이 해제됩니다.
- <span style="color:#0000FF">인자 owner_id 또는 group_id 중 하나가 -1이면 해당 ID는 변경되지 않습니다.</span>

- 패스네임에 파일명, 오너 아이디에 바꾸고자 하는 오너 아이디, 그룹 아이디에 바꾸고자 하는 그룹 아이디를 넣음
- 파일 유저와 그룹 오너를 바꿀 수 있다, 오너 아이디는 뉴 오너, 그룹 아이디는 뉴 그룹
- 하나만 바꾸고 싶으면 나머지 하나에 –1을 넣으면 안 바뀐다
- 자기가 소유한 파일만 오너를 바꿀 수 있다, 이 때 에러는 **EPERM**

파일의 소유자를 바꿔주는 시스템 콜입니다.(커멘드도 있습니다.)  
```c
$ chown user2 test
```
- user2에게 test 파일의 오너를 넘겨준다(자신의 소유 파일만 가능)

파일의 오너쉽이 바뀔 때 자동적으로 1이 0으로 턴오프(바뀐다)

## set-user-id and set-group-id permission are turned off for a file when the ownership of that file is altered.
스파이가 해킹 파일을 만들 때 체인지 오너(chown)에서 자동으로 rws를 rwx로 바꿔줘서 자정 작용을 합니다.  
즉 파일의 오너가 바뀔 때 셋 유저아이디 온 엑스큐션 퍼미션을 자동으로 오프시켜줍니다.(도둑질 방지)

> 권한이 s인 파일을 실행하면 유저 아이디가 super로 바뀌니까,  
그 유저 아이디로 파일을 맘대로 보고 바꾸고 실행할 수 있음  
심지어 체인지 오너(chown)로 파일의 소유권을 바꿀 수도 있으니 이를 방지함

# 3.2 File with multiple names
## File System
### File System (1/2)
- 파일 시스템은 저장 장치(하드 디스크 등)에 파일을 저장하기 위한 논리적인 구조를 가진 소프트웨어 구성 요소.
- 유닉스 파일 시스템은 계층 구조를 가진다 – 맨 위에 /(root)를 가진다, 폴더가 있다(윈도우에서는 디렉토리).
- 유닉스 파일 시스템에선 여러 개의 파일 시스템을 하나로 통합할 수 있다.
- 파일 시스템은 파일과 폴더의 이름을 지정하기 위한 명명 규칙을 지정한다.

### File System (2/2)
- Mount-on
    - 디렉토리는 마운트된 파일 시스템에 의해 커버됨
    - 마운트 테이블 및 vfs 목록
![그림12](https://ji-hun-park.github.io/assets/images/그림57.jpg "그림12"){: .align-center}
하나의 파일 시스템이 루트 파일 시스템으로 설정되고, 이 파일 시스템의 루트 디렉토리가 시스템 루트 디렉토리가 된다.  
다른 파일 시스템들은 기존의 트리에 있는 디렉토리에 마운팅 됨으로써 기존의 파일 시스템 구조에 부착된다.  
Mount-on된 디렉토리로의 모든 접근은 새롭게 마운트 된 파일 시스템의 루트 디렉토리의 접근으로 해석된다.

- 루트부터 시작(루트는 하나밖에 없음)
- 다른 파일 시스템에도 루트가 있는데, 이걸 통합하러면 마운트 시켜야 한다
- 루트는 하나만 존재할 수 있으므로 다른 파일 디렉토리를 루트와 통합하는 것을 마운트 시킨다고 한다(마운트 포인트)

## UNIX File System
### UNIX File System (1/2)
**파일 링크 개념을 이해하려면 UNIX 파일 시스템의 구조에 대한 개념적 이해가 필요합니다.**  
**다양한 UNIX 파일 시스템 구현이 오늘날 사용되고 있습니다. (UFS, PCFS, HSFS..)**  
![그림13](https://ji-hun-park.github.io/assets/images/그림58.jpg "그림13"){: .align-center}
- 파일 : 데이터를 위한 container
- 파일시스템: 사용자가 상이한 파일들을 구성, 조작하고 접근할 수 있도록 한다.
- 각 파일에 대해 파일 이름 이외에 여러가지 속성들을 유지한다. 이러한 속성들은 디렉토리 항에 저장되지 않고, i-node라고 하는 디스크 구조체에 저장된다

유닉스 파일 시스템에 있는 파일 시스템 종류는 다양합니다.  
유닉스 파일 시스템의 기본 틀은 4개의 블록으로 구성되어 있습니다.

### UNIX File System (2/2)
#### Block
- 보통 유닉스에서 블록의 크기는 ‘512Byte X 2의 배수’ 로 정의된다. (512,1024,2048)

#### Boot block
- 운영체제를 부트스랩(로드하고 초기화)할때 필요한 코드가 있다.
- 파일 시스템을 엑티베이트(활성)하는데 사용되는 부트 코드가 있는 부분
- 단 하나의 파티션만이 이러한 정보를 수록하며, 다른 파티션은 비어 있는 부트 영역을 가지게 됨.

#### Super Block
- 파일 시스템 자체의 속성과 메타데이터(정보)를 가지고 있음
    - 블록이 총 몇 개나 있는지
    - 비어있는 아이-노드 개수가 총 몇 개인지(inode free list의 i-node 수)
    - 데이터 블록중 비어있는(free blocks) 비트맵이 어디에 있는지
    - 디스크 같은 장치에서 I/O가 블록 단위인데, 블록 사이즈(바이트)가 얼마인지(보통 4K바이트)
    - 총 블록 중 비어있는 블록 수(free blocks)
    - 채워져 있는(사용된) 블록(blocks) 수 등

#### i-nodes
- i-node들이 들어가 있음
    - index node라는 말에서 유래됨
- 각 파일은 하나의 i-node가 할당(파일과 1대 1 대응, 파일을 유니크하게(고유하게) 아이덴티티함(식별함))
- 디스크에 있는 파일 들과 연결(연관)되어있음
- 파일에 관한 정보들을 가지고 있음
- i-node번호는 i-node리스트 내에서 인덱스와 동일하다
- s5fs에서 i-node크기는 64바이트임.
- blok당 여러 개의 i-node들이 들어가게된다.
- i-node 리스트는 고정된 크기(파티션 상에 파일 시스템이 구축되는 중에 구성)이기 때문에 한 파티션이 가질 수 있는 파일의 수가 제한된다.

#### data blocks
- 파일 블록을 저장하기 위한 것
- 블록 단위로 데이터(파일)가 들어있다(사용,비사용 둘 다)

## i-node
- 아이노드란 디스크의 데이터 블록에 파일이 있다면, 어떤 파일(레귤러,디렉토리,스페셜 등)이든지 그 파일에 대해 딱 하나만 반드시 대응이 된다
    - 최소한 하나의 디렉토리에서 링크되어야 함
    - 파일에 관한 정보들이 들어있다
- 디스크에 데이터가 있는 파일(일반, 디렉토리, 심볼릭 링크)은 또한 i-노드에서 가리키는 데이터 블록을 갖는다
- 파일에 연결된 아이노드의 일련번호로 파일을 지정할 수 있다
- 아이노드에 파일 명은 없다(디렉토리에 등록되어 있다, 최소 한 개의 디렉토리가 반드시 있어야 한다)
- 디렉토리에 있는 이름이 디스크에 어떤 파일을 가르키는 지에 대한 포인터가 있다
    - 디렉토리에는 파일 명과 아이노드 번호만 존재한다
- 아이노드의 번호는 0부터 시작해서 쭉 나가는데, 0과 1은 특수 목적 i-노드라 쓰지 않는다
    - 0번은 실제로는 쓰이지 않는다(i-노드가 없다는 의미)
    - 1번은 배드 블록(불량 디스크 블록, 에러가 있는, 데이터를 쓸 수 없는)들에 대한 정보가 있는 파일
    - 실제 파일 아이노드는 2번부터 시작하는데 2번은 루트 디렉토리(/)에 대응한다(예약되어 있음)

## Hard link & Symbol link
### Hard link & Symbol link (1/2)
**하드 링크**
- 하드 링크(Hard link)는 파일에 대한 직접(direct) 포인터(pointer)입니다.
- 링크 수는 i-노드를 가리키는 디렉토리 항목의 수입니다.
- 링크 수가 0이 될 때만 파일을 삭제할 수 있습니다.
- 이러한 유형의 링크를 **하드 링크**라고 합니다.
- 파일 시스템을 교차할 수 없음
- 슈퍼유저만 디렉토리에 대한 하드 링크를 만들 수 있습니다.

![그림14](https://ji-hun-park.github.io/assets/images/그림59.jpg "그림14"){: .align-center}
아이노드는 번호가 있고 파일에 엑세스하기 위해선 이름이 필요한데, 이 이름은 디렉토리 안에 있습니다.  
디렉토리 안에 파일 명과 아이넘버가 있는데 file1의 아이넘이 97이다 이런식으로 알 수 있습니다(하드 링크).  
파일에 엑세스하기 위해선 그에 대응되는 i-노드에 대응되는 파일 명이 적어도 하나가 있어야 합니다(1개 이상).  
즉, 또 다른 이름의 하드 링크가 있을 수 있습니다.

i-노드에 대해서 연결된 파일 이름(하드 링크)의 개수를 링크 카운트라고 합니다.  
링크 카운트가 0이 될 때는 파일이 딜리트(delete, 제거) 되었다는 것을 뜻합니다.  
하드 링크는 파일 시스템을 크로스할 수 없다 - 유닉스 시스템 안에는 파일 시스템이 여러 개 존재할 수 있는데, 서로 다른 2개의 시스템이 있을 때 하나의 루트를 다른 하나의 유저(디렉토리)에 마운트해서 하나처럼 쓸 수 있습니다.  
그렇다고 해서 서로 다른 시스템끼리 하드 링크를 연결하는 것은 안 됩니다.

슈퍼 유저만이 디렉토리에 관한 하드 링크를 만들 수 있습니다.(처음에 만드는건 누구나 가능하지만 그 디렉토리 파일에 추가로 하드 링크를 달 때)  
디렉토리에 i-노드 주소가 있고, i-노드에 데이터 블록 주소가 있습니다.

### Hard link & Symbol link (2/2)
**심볼 링크**
- 심볼릭 링크(Symbolic link)는 파일에 대한 간접(indirect) 포인터입니다.
- 심볼 링크의 실제 내용은 링크된 파일입니다.
- 파일 시스템 제한 없음
- 하드 및 심볼 링크에서 하드 링크가 생성되지만
    - 심볼 링크에 의해 생성된 하드 링크는 경로 문자열이 포함된 파일에 대한 것입니다.

![그림15](https://ji-hun-park.github.io/assets/images/그림60.jpg "그림15"){: .align-center}
- 윈도우즈에 바로가기 파일이 있는데 거기에 해당 파일에 대한 패스네임이 있다.
    - 그 패스네임을 보고 파일에 링크가 되도록 하는데 이게 유닉스에선 심볼 링크 파일에 대한 간접 포인터이다.
- 심볼 링크 안 실제 내용은, 파일에 링크된 패스 네임이 들어있다.
- 심볼 링크는 제한이 없다(다른 파일 시스템에 크로스할 수 있음).
- 심볼 링크는 링크(link) 명령은 못 쓰고 ln이랑 –s만 쓸 수 있다.
- dirA name1이 원래 파일, dirB name2가 심볼 링크 파일
- 심볼 링크는 아예 별개의 파일을 하나 생성한다.
- 파일 안에 내용에 패스네임(경로)가 있어서 그걸 보고서 찾아간다.
- 파일 명 뒤에 화살표로 패스네임을 나타내는 형태, 앞에 <span style="color:red">l</span>로 심볼릭 링크임을 나타냄

## The link(2) system call
```c
#include <unistd.h>

int link(const char *orginal_path, const char *new_path);

//Returns: 0 if OK, -1 on error
```
- 새 디렉토리 항목 생성 및 링크 카운트 증가.
- 디렉토리에 대한 하드 링크 생성
    - 슈퍼유저로만 제한됩니다.(파일 시스템 루프 때문에)
- Arguments(인자들)
    - 두 경로 이름이 모두 동일한 파일 시스템에 있어야 합니다.

```c
link(“/usr/keith/chap.2”, “/usr/ben/2.chap”);
```
링크는 하드 링크와 심볼릭 링크를 만드는 시스템 콜입니다.(명령도 link가 있다, 오리지널패스, 뉴패스)

첫 아규먼트(인자)는 오리지널 패스네임, 두 번째는 뉴링크 패스네임, 성공하면 0, 실패하면 -1을 리턴합니다.  
오리지널 패스네임은 파일에 관한 패스네임, 뉴는 새로 링크되는 이름입니다.(디렉토리 엔트리에 새롭게 추가된다, i-노드에 관한 링크 카운터가 하나 증가한다)  
디렉토리에 대한 하드 링크를 추가하는 것은 슈퍼 유저만이 가능합니다.(일반 유저는 처음만 가능)

둘 중 밑의 코드는 사용 예

## The unlink(2) system call
![그림16](https://ji-hun-park.github.io/assets/images/그림66.jpg "그림16"){: .align-center}
```c
#include <unistd.h>

int unlink(const char *pathname);

//Returns: 0 if OK, -1 on error
```
- 기존 디렉토리 항목 제거(링크 삭제)할 때 사용
    - 삭제하고자 하는 패스 네임만 넣으면 된다.
    - 그 이름이 디렉토리에서 제거됨
- 명명된 링크만 제거하고 파일의 링크 수를 하나 줄임
- 파일에 대한 다른 링크가 있는 경우 파일의 데이터는 다른 링크를 통해 계속 액세스할 수 있습니다.
- 링크 수가 0으로 감소하면 디스크 블록이 사용 가능한 블록 목록에 추가됩니다.

name2라는 엔트리를 지우면 자동적으로 링크가 사라집니다.(i-노드 링크 카운트 1 감소)  
이름을 i-노드 정보와 함께 삭제하는 것에 불과합니다.

어떠한 i-노드의 존재 의의가 있으려면 링크 카운트가 적어도 하나는 있어야 합니다.  
그런데 언링크를 계속하다보면 i-노드의 링크 카운트가 0이 될 때가 있습니다.  
이럴 경우 엑세스 불가(이름이 없기에)하고 의미가 없기 때문에 삭제 됩니다.  
i-노드는 프리(free) i-노드가 되고, i-노드가 가리키는 블록의 데이터 들은 empty가 됩니다.(클리어)  
디스크 블록 안에 추가된 리스트들은 프리(free) 블록안에 들어가게 됩니다.

- 링크 해제 권한?
    - 언링크 퍼미션은 라이트 퍼미션이 필요함

### example
```c
/* move -- 한 파일을 하나의 경로이름으로부터 다른 경로이름으로 옮긴다. */
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>

char *usage = "usage: move file1 file2\n";

/* main은 명령줄에 의해 표준적인 방법으로 전달된 인수를 사용한다. */
main (int argc, char **argv){
 if (argc != 3){
         fprintf (stderr, usage); exit (1);
 }

 if ( link (argv[1], argv[2]) == -1){
 	perror ("link failed");
 	exit (1);
 }

 if ( unlink(argv[1]) == -1){
 	perror ("unlink failed");
 	unlink (argv[2]);
 	exit (1);
 }
 printf ("Succeeded\n");
}
```
명령을 쓸 때 `$ a.out arg1, arg2, arg3` 이런 문자열을 셸이 읽고 파싱(parsing)을 합니다.  
문자열 사이 공백이 딜리미터(delemeter, 구분자)라 각각을 파악 합니다.  
문자열을 분리해서 만들어 놓습니다.  
argc는 나눠 놓은 것들의 개수, 여기선 4(파일명 + 뒤에 인자들)입니다.

문자열엔 포인터가 있고 이런 문자열 포인터들의 배열이 필요합니다.  
```c
int* X; X = (int*)malloc(sizeof(int),100);
```
이게 int X[100];과 같습니다.

즉 어레이(array, 배열)는 포인터가 어레이가 되므로, 포인터에 어레이를 쓰려면 ***가 하나 더 있어야 합니다**(이중 포인터).

이 경우엔 argv[0], argv[1], argv[2], argv[3]이 있습니다.  
argv는 이 배열의 첫 번째 주소를 가리킵니다.  
각 인덱스들은 각각의 문자열을 가리킵니다.(캐릭터 포인터형 배열)  
argc는 argv들의 개수만큼 크기입니다.

캐릭터(char)의 포인터를 가리키는 포인터니까, 포인터의 포인터(*이 2개 들어감)

>포인터가 보물의 위치를 나타내는 지도라면
>> 이중 포인터는 그 지도가 묻혀있는 곳을 나타내는 비밀 문서입니다.

argc가 3이 아니면 exit합니다.  
즉, argc가 3일 때만 다음으로 넘어갑니다.(명령어와 아규먼트(인자) 2개)  
argv[0]가 명령어니까 [1], [2]는 아규먼트1, 아규먼트2 입니다.

첫 아규먼트에 오리지날 파일, 두 번째 아규먼트에 뉴 링크(오리지널 파일에 새 하드 링크가 추가 된다)를 넣습니다.  
기존 연결된 링크가 새로운 링크로 바뀝니다.  
언링크로 기존 링크를 지웁니다.  
이게 바로 파일 이름을 바꾸는 코드입니다.

## The remove(2) system call
```c
#include <stdio.h>

int remove(const char *pathname);
//Returns: 0 if OK, -1 on error
```
- 파일의 경우 remove는 unlink와 동일합니다.
- ISO C는 파일을 삭제하기 위한 remove 함수를 지정했습니다.
- C 표준을 구현하는 대부분의 비 UNIX 시스템이 당시 파일에 대한 링크 개념을 지원하지 않았기 때문에 역사적인 UNIX 이름인 unlink에서 변경되었습니다.

리무브 시스템 콜 = 언링크 시스템 콜  
`stdio.h` include하고 int remove(const char *pathname);(성공하면 0, 실패하면 –1 리턴)  
처음에 링크를 만들고 언링크를 만들었는데, 그 기능과 이름이 서로 잘 맞지 않아서 추가했을 것이라 추정됩니다.

## The rename(2) system call
```c
#include <stdio.h>

int rename(const char *oldname, const char *newname);

//Returns: 0 if OK, -1 on error
```
- rename 함수로 파일이나 디렉토리의 이름을 바꿉니다.
- 이 함수는 ISO C에서 파일을 위해 정의합니다. (C 표준은 디렉토리를 다루지 않습니다.) POSIX.1은 디렉토리와 심볼릭 링크를 포함하도록 정의를 확장했습니다.
- Arguments(인자들)
    - oldname과 newname이 같은 파일을 참조하는 경우 함수는 아무것도 변경하지 않고 성공적으로 반환합니다.

`stdio.h` include하고, int rename(const char *oldname, const char *newname); (0, -1 리턴)  
원래는 위에 [예제](https://ji-hun-park.github.io/linux/Linux-Theory-03/#example-3)처럼 코드를 짰으나 이런 기능을 자주 이용하다 보니 나중에 만들어 졌습니다.

## The symlink(2) system call
## The readlink(2) system call
# 3.3 Obtaining file information: *stat* and *fstat*
## The stat(2) system call
### The stat(2) system call (1/2)
### The stat(2) system call (2/2)
### example
## 작성중
![그림17](https://ji-hun-park.github.io/assets/images/그림62.jpg "그림17"){: .align-center}
![그림18](https://ji-hun-park.github.io/assets/images/그림63.jpg "그림18"){: .align-center}
![그림19](https://ji-hun-park.github.io/assets/images/그림64.jpg "그림19"){: .align-center}
![그림20](https://ji-hun-park.github.io/assets/images/그림65.jpg "그림20"){: .align-center}
## 마무리
이상으로 Linux 이론의 파일2편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
