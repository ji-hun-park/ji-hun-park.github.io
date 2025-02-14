---
title:  "Linux 기초 이론 04 DIRECTORIES"
excerpt: "Linux 기초 이론 네 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-02-14T24:00:00-23:59
---

# Linux Lecture Theory 04 DIRECTORIES
## Introduction
### Directories, file systems and special files
이전 두 장에서는 UNIX 파일 구조의 기본 구성 요소인 일반 파일에 집중했습니다.  
이 장에서는 파일 구조의 다른 구성 요소인 디렉터리, 파일 시스템, 특수 파일을 살펴봅니다.

# 4.3 The implementation of a directory
## Directory
### Directory (1/2)
- 디렉터리 항목(entries)이 들어 있는 파일.
    - 디렉터리 엔트리란 파일과 디렉터리 목록을 갖고 있는 파일.
- 파일에 사용되는 많은 시스템 콜은 디렉터리를 조작하는 데 사용될 수 있음.
    - 몇가지 차이점이 존재함.
- 디렉터리와 일반 파일의 차이점
    - 디렉터리는 creat 또는 open 시스템 콜을 사용하여 새로 만들 수 없음.
        - 오픈해서 내용을 볼 수 없음.
- 또한 open은 O_WRONLY 또는 O_RDWR 플래그가 설정된 경우 디렉터리에서 작동하지 않음.(errno = **EISDIR**)
    - 오픈을 못 쓰기에 디렉터리는 또 다른 시스템 콜이 있음.
- write를 사용하여 디렉터리를 업데이트하는 것은 불가능함.
    - 커널만 디렉터리에 쓸 수(write) 있음.

파일에 대한 디렉터리 안의 entry를 그 파일에 대한 하드링크라 한다.  
디렉터리는 디렉터리 엔트리들을 포함하는 파일이다.  
디렉터리와 파일의 차이점은 다음과 같다.  
- 디렉터리는 creat혹은 open 시스템 콜을 이용하여 생성할 수 없다.
- 디렉터리는 O_WRONLY나 O_RDWR플레그를 이용하여 오픈할 수 없다. 왜냐하면 디렉토리 구조가 엉망이 될 수 있기 때문이다.
- 따라서 write를 이용하여 directory를 변경하는 것은 불가능하도록 되어 있다.
- 디렉터리의 write는 오직 커널만이 가능하다.

### Directory (2/2)
- 디렉터리 항목(entries) 시리즈(series)로 구성되며, 각 파일 또는 그 안에 포함된 하위 디렉터리마다 하나씩 있음.
    - 디렉터리 엔트리는 파일명이 연달아 있다.
    - 파일과 하위 디렉터리 각각에 대해 엔트리가 있음.
- 디렉터리 항목은 다음으로 구성됨.
    - inode 번호
    - 문자 필드(character field)

![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG022.jpg "그림01"){: .align-center}
> inode번호 / 파일명(디렉터리명) / null

S5fs디렉터리의 경우, 디렉터리 엔트리는 16바이트의 고정크기 레코드로 구성됨.  
- inode number(2byte)
- name(14byte) → 14문자로 파일이름이 제한됨

Inode number가 0이면, 이것은 해당파일이 더 이상 존재하지 않음을 의미.

## link and unlink revisited
각 링크는 단순히 원본과 동일한 inode 번호와 새 이름을 가진, 새 디렉터리 슬롯을 생성합니다.  
```
 link(“abc”, “xyz”);
```
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG023.jpg "그림02"){: .align-center}
`unlink` 시스템 콜을 사용하여 링크를 제거하면,  
이 파일 이름이 파일에 대한 마지막 링크를 나타내는 경우 전체 inode 구조가 지워집니다.

- 링크(하드링크하기), 언링크(하드링크지우기)
    - 디렉터리 엔트리는 하드링크로 보는 것이 맞다.
- 링크 시스템 콜을 사용해 저 명령(위의 코드) 실행(오리지널파일, 뉴파일) - 새로 파일 추가함
- 기존이랑 i노드 번호가 똑같은 엔트리 추가(링크 카운트 추가)
- 언링크로 링크를 지우면 아이노드에 대한 링크 카운트가 0이 될 때가 있는데
    - 이러면 아이노드가 가리키는 파일이 아무 의미가 없어지므로 클리어(제거)된다.

## Dot and double-dot
>‘.’ &nbsp;:&nbsp;  current working directory  
‘..’  &nbsp;:&nbsp;  parent directory

- .은 현재 디렉터리, ..은 부모 디렉터리를 나타낸다.
- .은 자기 자신을 가리킨다, ..은 그 상위 디렉터리를 가리킨다.

![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG024.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG025.jpg "그림04"){: .align-center}

## example
### example (1/2)
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG026.jpg "그림05"){: .align-center}
아이노드 배열 / 데이터 블록  
아이노드 배열에 0번부터 쭉 있습니다.  
위 그림에서 보면 testdir은 오른쪽 디렉터리의 하위(자식) 디렉터리임을 알 수 있다.

### example (2/2)
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG027.jpg "그림06"){: .align-center}
- cat은 파일들을 합쳐서 복사하는 명령(한 개면 단순 카피해서 옮기기).
    - 위 이미지에서 파란색 네모 안에 cat과 > 사이에 KBD가 빠짐.
    - cat f1 f2 fn > fout
    - cat f1 > fout
- cat > out 이 경우엔 키보드가 된다.
- ^D(컨트롤D)는 엔드 오브 파일(EOF), 입력이 끝나면 테스트1이라는 파일이 새로 생성된다.
- mkdir로 디렉터리 생성

디렉터리의 링크카운트는 처음 생성 시 무조건 2가 된다.  
그 이유는 디렉터리를 생성한 위치에서의 디렉터리 엔트리와 자기 자신을 가리키는 .에 의해서이다.  
그리고 새로 생성된 디렉터리는 ..이 있기 때문에 상위 디렉터리의 링크카운트를 1늘린다.

## Directory permissions
### Directory permissions (1/3)
디렉터리 권한(permissions)은 일반 파일 권한과 정확히 같은 방식으로 구성됩니다.  
그러나 해석은(의미는) 다소 다릅니다.

- 리드 퍼미션 (read permission, 읽기 권한)
    - 파일 또는 하위 디렉터리 이름 나열(list)을 읽는 권한(안의 목록을 볼 수 있음)
- 라이트 퍼미션 (write permission, 쓰기 권한)
    - 새 파일 생성, 기존 파일 제거, 이름 바꾸기 권한
        - 삭제나 생성은 단순히 엔트리를 추가하거나 삭제하는 것으로 이해하면 직관적으로 이해하는데 도움이 된다.
- 엑스큐트 퍼미션(서치 퍼미션) (execute permission, 실행 권한(search permission, 검색 권한))
    - 프로그램 내에서 디렉터리 이동(`chdir` 시스템 콜), 커렌트(current, 현재) 디렉터리로 잡을 수 있는 권한
        - 디렉터리 내에 object들을 검색하는 권한을 말한다.

### Directory permissions (2/3)
예를 들어, **/usr/include/stdio.h** 파일을 열려면 실행 권한이 필요합니다.  
```
 /,/usr,/usr/include
```
- 디렉터리에 대한 읽기 권한과 디렉터리에 대한 실행 권한은 다른 것을 의미합니다.
    - 읽기 권한: 디렉터리에 있는 모든 파일 이름 목록을 얻는 것
    - 실행 권한: 디렉터리를 통과(pass)함(특정 파일 이름을 찾으려면 디렉터리를 검색해야 함).

이것이 디렉터리에 대한 실행 권한 비트를 종종 검색 비트라고 하는 이유입니다.

예를 들어 `/usr/include/stdio.h`파일을 open하기 위해선 각 디렉터리에 대해 실행 권한이 있어야 한다.  
디렉터리의 read 퍼미션과 execute 퍼미션은 분명히 다름을 기억해야한다.  
read는 파일목록을 얻기 위한 권한이고, execute 권한은 디렉터리 내에 특별한 파일을 찾기 위해 디렉터리를 검색하는 권한이다.

### Directory permissions (3/3)
- Save-text-image(스티키 비트) - **S_ISVTX**
- 디렉터리에 이 비트가 설정된 경우
    - 디렉터리의 파일은 사용자에게 디렉터리에 대한 쓰기 권한이 있고 다음 중 하나가 있는 경우에만 제거하거나 이름을 바꿀 수 있습니다.
        - 파일 소유자(Owner)
        - 디렉터리 소유자(Owner)
        - 슈퍼유저(Superuser)
- 다른 사용자는 다른 사람이 소유한 파일을 삭제하거나 이름을 바꿀 수 없습니다.

save-text-image 비트가 디렉터리에 설정되면 특별한 의미를 가진다.  

다음을 가정하자, 어느 특정 그룹에서 소속되는 그룹 사용자들이 공용으로 사용하는 디렉터터리가 있다고 가정하자.  
이때 이 디렉터리의 권한이 rwxrwx---로 설정되어 있다고 하자.  
이때 이 그룹의 a라는 사용자는 그룹의 write권한이 있으므로 file을 작성할 수 있다.  
이때 작성한 파일을 file1이라 하자.  
이때 그룹의 b라는 사용자는 그 디렉터리에 w권한이 있으므로 a가 작성한 file을 삭제할 수 있다.  
이러한 상황은 a사용자가 원하지 않을 수 있다.  

디렉터리에서 이러한 설정을 막기 위해 save text image bit를 설정한다.  
이 비트가 설정된 디렉터리의 파일은 파일 소유자 혹은 디렉터리 소유자만이 파일을 지울 수 있다.  
물론 수퍼유저는 파일 삭제가 가능하다.  
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG028.jpg "그림07"){: .align-center}
ls(리스트)를 l옵션(롱 포맷)에 a 옵션으로 히든 파일(.과 ..등)까지 본다.  
파일 형식, 퍼미션, 오너아이디, 그룹아이디, 파일 크기, 날짜, 파일명 순이다.  

파일을 오픈할 때(인클루드도) 그 상위 디렉터리에 X 퍼미션이 필요(그 파일 자체도 리드 퍼미션 필요)하다.  
상위 디렉터리 중에 하나라도 X 퍼미션이 없으면 오픈이 불가하다.  

디렉터리에서 리드와 X 퍼미션은 서로 다르다.  
리드는 디렉터리 목록을 **볼 수만** 있고, X는 그 디렉터리 자체로 들어 갈 수 있는 권한(서치 퍼미션이라고도 한다)이다.  
(검색할 때 다른 디렉터리를 거쳐가야 하므로)

`st_mode`에서 앞 4비트는 파일 타입, sgt rwx(u) rwx(g) rwx(o)  
여기서 t(sticky bit, 스티키 비트)가 파일에선 의미가 없지만, 디렉터리에서 1로 설정되면 의미가 있다.

디렉터리에 있는 파일을 삭제하거나 이름을 바꾼다는 것은 디렉터리에 대해 라이트 퍼미션이 있어야 하는데, 스티키 비트가 있으면 라이트 퍼미션 뿐 아니라 다음 중 하나가 더 있어야 한다 – 파일의 오너거나, 디렉터리의 소유자거나, 슈퍼유저거나  
셋 유저 아이디 온 엑스큐션은 유저 퍼미션 x가 s로 바뀌고, 셋 그룹 온 엑스큐션은 그룹 퍼미션 x가 g로 표시되고, 스티키 비트는 아더스(others) x가 t로 바뀐다.

# 4.4 Programming with directories
디렉터리와 관련된 프로그래밍을 위해 몇가지 자료구조와 시스템 콜을 알아야 합니다.

## The dirent structure
```c
#include <dirent.h>

struct dirent {
        ino_t d_ino;                /* i-node number */
        char  d_name[NAME_MAX + 1]; /* null-terminated filename */
      }
```
`d_ino`의 값이 0이면 디렉터리에 빈 슬롯이 있음을 나타냅니다.

`dirent` 구조체, 디렉터리 엔트리는 파일명과 아이노드 번호로 이루어졌음.  
`<dirent.h>` 인클루드 후,  
>struct dirent { ino_t d_ino;	char d_name[NAME_MAX + 1]; }

첫 번째 인자는 아이노드 번호 두 번째 인자는 파일명(+1은 마지막 널 문자)  
ino_t는 long 또는 int 혹은 unsigned

## The mkdir(2) system call
```c
#include <sys/stat.h>

int mkdir(const char *pathname, mode_t mode);

//Returns: 0 if OK, -1 on error
```
명령어와 똑같이 시스템 콜에도 mkdir이 있다.  
system 밑에 stat 헤더 인클루드하고, 첫 번째 인자는 패스네임, 두 번째 인자는 퍼미션, 성공 시 0, 실패 시 -1 리턴

퍼미션은 프로세스의 umask(퍼미션 제한) 적용, mkdir로 디렉터리를 만들면 두 개의 링크 .과 ..이 디폴트(default)로 만들어진다.  
보통은 r, w 권한만 주지만 디렉터리는 x 권한을 줘야 체인지 디렉터리(cd)가 가능하다.  
```c
$ mkdir dir
$ cat > dir/file.txt
test test test ^D
$ chmod 666 dir
```
첫 번째 예제  
dir이란 이름의 디렉터리를 새로 만들고 cat으로 키보드를 통해 입력 받은 값으로 dir 밑에 file.txt란 파일을 새로 만든다.  
test 3번 입력하고 컨트롤D(^D)하면 파일이 생성된다.  
체인지모드(chmod) 666으로 디렉터리 권한을 변경하면 rw-rw-rw- 로 퍼미션이 변경된다.  
```c
$ ls dir   // read entries
dir/file.txt
$ cat dir/file.txt
cat: dir/file.txt: 허가 거부됨
```
ls 명령어로 dir에 있는 엔트리 들은 볼 수 있다.  
하지만 캣(cat) 명령은 실행 불가하다.  
그냥 `cat file`하면 디폴트(default)는 스크린(screen, 모니터)  
>cat file (> screen)  

cat하려면 파일을 오픈해서 열어야하므로 불가하다.  
```c
$ chmod 111 dir
$ ls dir  :허가 거부됨
$ cat dir/file.txt
test test test
```
체인지모드(chmod)로 111로 퍼미션을 변경(셋 다 x 퍼미션만 있음)한다.  
ls는 불가하지만 cat은 가능해진다.

디렉터리 생성된 파일과 마찬가지로 프로세스의 umask에 영향을 받는다.  
디렉터리에 퍼미션을 설정할때 가장 많이 하는 실수는 파일과 동일하게 read와 write퍼미션 만을 설정하는 것이다.  
그러나 디렉터리 내에 file에 접근하기 위해서는 디렉터리에 대한 실행권한이 필요함을 알아야 한다.

## The rmdir(2) system call
```c
#include <unistd.h>

int rmdir(const char *pathname);

//Returns: 0 if OK, -1 on error
```
디렉터리를 삭제하기 위해서는 오직 . 과 .. 두 개의 파일만이 존재해야 합니다.  
빈 디렉터리는 rmdir 함수로 삭제됩니다.  
빈 디렉터리는 dot과 dot-dot에 대한 항목만 포함하는 디렉터리라는 점을 기억하세요.

- $ rmdir dir로 디렉터리를 지울 수 있음(단, 밑에 파일이 하나도 없을 때만 가능)
- 명령어와 같은 이름의 시스템 콜 rmdir, 우선 `<unistd.h>` 인클루드
- int rmdir(const char *pathname); 성공 시 0 실패 시 –1 리턴, 패스네임은 디렉터리명
- 디렉터리만 지울 수 있다, 닷(점), 닷닷(점점)을 제외한 파일이 있으면 지울 수 없음

## The opendir(3)
```c
#include <dirent.h>

DIR *opendir(const char *dirname);
 
//Returns: pointer if OK, NULL on error
```
`DIR`은 표준(Standard) I/O 라이브러리에서 사용되는 FILE type(FILE Pointer)과 비슷한 방식으로 작동합니다.  
항상 널 포인터를 테스트하기 위해 적절한 오류 검사 코드를 작성해야 합니다.  
프로그램이 디렉터리에 대한 접근을 마치면 닫아야 합니다.  
이는 `closedir` 함수로 할 수 있습니다.

- (3)은 라이브러리
- `<dirent.h>` 인클루드하고,
- DIR *opendir(const char *dirname); 성공하면 포인터, 실패하면 널 리턴
- 그냥 오픈을 쓰면 **EISDIR** 에러넘버 나오면서 오픈에 실패한다
- 디렉터리 패스네임을 집어 넣으면 디렉터리 포인터를 리턴해 줌
- **DIR**은 스탠다드(표준) I/O 라이브러리에서 정의된 **FILE**
- 오픈에 실패하면 널 포인터가 리턴된다
- close도 시스템 콜이 아닌 closedir 라이브러리 사용

## The closedir(3)
```c
#include <dirent.h>

int closedir(DIR *dirptr);

//Returns: 0 if OK, -1 on error
```
`closedir` 함수는 인자 `dirptr`가 가리키는 디렉터리 스트림을 닫는다.  
```c
#include <stdlib.h>
#include <dirent.h>

main(){
   DIR *dp;
   if ((dp = opendir("/tmp/dir1")) == NULL){
      fprintf (stderr, "Error on opening directory /tmp/dir1\n");
      exit(1);
   }

   /* 디렉터리에 대한 코드를 처리한다. */
   closedir(dp);
}
```
인자로 `opendir`에서 나오는 디렉터리 포인터 값을 넣는다.

## The readdir(3)
```c
#include <dirent.h>

struct dirent *readdir(DIR *dp);

//Returns: pointer if OK, NULL at end of directory or error
```
첫 번째 `readdir`에서 첫 번째 디렉터리 항목(entry)이 struct dirent로 읽혀집니다.  
`readdir` 완료 시 directory pointer는 다음 엔트리로 이동합니다.  

읽을 때도 `readdir` 이라는 라이브러리 사용, `<dirent.h>` 인클루드하고,  
struct dirent *readdir (DIR *dp); 성공하면 디렉터리 포인터, 실패(또는 EOD, 엔드 오브 디렉터리)하면 널 리턴

디렉터리 엔트리 구조체 안에 dino, dname 2개의 맴버 변수가 있다.  
읽으면 엔트리 하나가 넘어온다,  
파일 오프셋이 읽은 만큼 이동하면서 다음 엔트리를 가리킨다.

## The rewinddir(3)
```c
#include <dirent.h>

void rewinddir(DIR *dp);

//Returns: 0 if OK, -1 on error
```
`rewinddir` 호출에 이어 다음 `readdir`은 `dp`가 가리키는 디렉터리의 첫 번째 항목을 반환한다.

`rewinddir`(리와인드 디렉터리, 디렉터리 되감기) 라이브러리 - `<dirent.h>` 인클루드하고,  
void rewinddir(DIR *dp); 성공하면 0, 실패하면 –1 리턴  
read를 쭉하다가 엔드 오브 엔트리를 만나면 더 이상 못 읽는데, 포인터를 다시 처음으로 초기화한다.

## example
### example (1/2)
```c
#include <dirent.h>

int my_double_ls (const char *name){
   struct dirent *d;
   DIR *dp;

   /* 디렉토리를 개방하고, 실패여부를 점검함 */
   if ((dp=opendir(name)) == NULL)
   return (-1);

   /* 디렉토리를 살피면서 루프, 이때 inode번호가 유효하면 디렉토리항을 프린트 */
   while (d = readdir(dp)){
      if (d->d_ino !=0)
         printf ("%s\n", d->d_name);
   }

   rewinddir(dp); /*이제 디렉토리의 시작으로 되돌아간다 ... */
 
   while (d = readdir(dp)){/* ... 그리고 디렉토리를 다시 프린트한다. */
      if (d->d_ino != 0)
         printf ("%s\n", d->d_name);
   }
  closedir (dp);
}
```

### example (2/2)
```c
#include <stdio.h> /* NULL을 정의 */
#include <dirent.h>
#include <string.h> /* 스트링 함수를 정의 */
int match(const char *, const char *);

char *find_entry(char *dirname, char *suffix, int cont){
   static DIR *dp=NULL;		struct dirent *d;

   if (dp == NULL || cont == 0){
      if (dp != NULL)
         closedir (dp);
      if ((dp = opendir (dirname)) == NULL)
         return (NULL);
   }

   while (d = readdir(dp)){
      if (d->d_ino == 0)
         continue;
      if (match (d->d_name, suffix))
         return (d->d_name);
   }
   closedir (dp);
   dp = NULL
   return (NULL);
}

int match (const char *s1, const char *s2)
{
   int diff = strlen(s1)- strlen(s2);

   if (strlen(s1) > strlen(s2))
      return (strcmp(&s1[diff], s2) == 0);
   else
      return (0);
}
```
![그림16](https://ji-hun-park.github.io/assets/images/LNXIMG037.jpg "그림16"){: .align-center}

## 작성중
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG029.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG030.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG031.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG032.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG033.jpg "그림12"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/LNXIMG034.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/LNXIMG035.jpg "그림14"){: .align-center}
![그림15](https://ji-hun-park.github.io/assets/images/LNXIMG036.jpg "그림15"){: .align-center}

## 마무리
이상으로 Linux 이론의 디렉터리편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
