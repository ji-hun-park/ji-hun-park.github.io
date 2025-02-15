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

디렉터리 엔트리 구조체 안에 d_ino, d_name 2개의 맴버 변수가 있다.  
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

   /* 디렉터리를 개방하고, 실패여부를 점검함 */
   if ((dp=opendir(name)) == NULL)
   return (-1);

   /* 디렉터리를 살피면서 루프, 이때 inode번호가 유효하면 디렉터리항을 프린트 */
   while (d = readdir(dp)){
      if (d->d_ino !=0)
         printf ("%s\n", d->d_name);
   }

   rewinddir(dp); /*이제 디렉터리의 시작으로 되돌아간다 ... */
 
   while (d = readdir(dp)){/* ... 그리고 디렉터리를 다시 프린트한다. */
      if (d->d_ino != 0)
         printf ("%s\n", d->d_name);
   }
  closedir (dp);
}
```
double_ls(목록을 한 번 출력하고 같은 목록을 한 번 더 출력하는 기능)

디렉터리 엔트리 구조체형 포인터 d가 있고, 디렉터리 포인터 dp가 있다.  
디렉터리 이름을 받아서 오픈해서 디렉터리 포인터를 받고(널이면 리턴 –1),  
성공하면 d에다 dp를 읽기(readdir)해서 i넘버가 0이 아니면 읽은 이름을 출력한다.  
이걸 EOD(널이면 0)까지 반복하고, 다시 리와인더(rewinddir)로 오프셋을 초기화 한다, 맨 처음부터 다시 읽는다.  
다시 앞에 거를 반복, 널이 리턴되면 false라 종료된다, 이후 디렉터리를 닫고 프로그램을 종료한다.

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
`dirname`이란 폴더(디렉터리) 아래 파일에서 주어진 접미사(suffix)로 끝나는 첫 번째 파일을 찾는 것이 find_entry 함수의 기능이다.  
aaa bbb.txt ccc.txt dddd 가 있을 때 *suffix 값을 “.txt”로 주면 저걸로 끝나는 파일을 찾는데,  
기본은 첫 번째를 찾고, cont로 순서를 바꾼다(cont가 1이면 다음으로 가고, 0이면 처음 거).

dp와 d는 본래 지역 변수(해당 펑션 안에서만 유효, 펑션 실행 시 메모리가 생성, 펑션 종료 시 날아간다)인데,  
static을 통해 펑션 실행 전에 미리 메모리를 할당해서 펑션을 실행하고, 나갔다 들어와도 전의 값을 그대로 갖고 있는다.

처음 실행 시 초깃값이 널이므로 오아(or) 뒤에 것은 안 보고, `opendir`로 오픈한다.  
널이 아니면 디렉터리 엔트리를 읽어들인다.  
ino가 0이면 없는 엔트리니 다음으로 스킵한다.  
이름과 서픽스를 비교해 서픽스가 있는지 체크한다.  
서픽스를 찾으면 네임을 리턴한다.  
하나도 못 찾으면 `closedir`한다.

두 번째 들어올 때는 dp가 널이 아니다(스태틱 때문에), cont가 0이므로 true(cont가 0이 아니면 close해서 다시 찾는다).  
cont가 1이면 저걸 skip하고 다음 걸 읽는다.

match는 두 번째 인자 suffix, 첫 번째 인자 파일명.  
우선 변수 diff로 두 캐릭터 배열의 length(마지막 널은 포함 안 함)를 뺀다.  
그러면 앞의 길이만 남는다.(4)  
s1이 더 길다는 전제 조건 하에 스트링컴페어(strcmp)를 한다.(두 문자열이 같은지 비교하는 함수)  
여기서 diff만큼 뒤에서 point해서 주소를 주고, 둘이 같으면(0이 되면) true를, 아니면 false를 return한다.

## The current working directory
각 UNIX 프로세스에는 자신의 고유한 현재 작업 디렉터리(current working directory)가 있습니다.  
커런트 디렉터리 혹은 워킹 디렉터리라고도 합니다.  
사용자와 명확하게 연관된 것으로 보이는 현재 디렉터리는 실제로 해당 사용자의 명령을 해석하는 셸 프로세스와 연관된 현재 작업 디렉터리입니다.  
셸에 커멘드를 치면 이게 프로세스가 되어, 처음 프로세스의 현재 작업 디렉터리는 프로세스가 시작된 프로세스의 현재 작업 디렉터리로 설정됩니다.  
(커멘드를 해석하고 있는 쉘 프로세스가 작동하는 현재 디렉터리)  
초기에 어떤 프로세스의 워킹 디렉터리는 보통 셸 워킹 디렉터리랑 같아집니다.

- 유닉스 프로세스는 자신만의 current working directory를 가진다.
- 사용자와 관련된 current directory는 실제로는 사용자의 명령을 해석하는 shell process의 current working directory이다.
- 대부분  process의 current working 디렉터리는 사용자의 명령을 입력받아서 프로세스를 실행시킨 셸 프로세스의 current working 디렉터리가 상속된다.

## The chdir(2) system call
```c
#include <unistd.h>

int chdir(const char *path);

//Return: 0 if OK, -1 on error
```
- 이 변경 사항은 `chdir`을 만드는 프로세스에만 적용됩니다.(current working directory 변경)
    - 다른 프로세스에 영향을 미치지 않는다.
- 오류
    - `path`가 유효한 디렉터리를 정의하지 않음
    - 실행 권한이 모든 구성 요소 디렉터리에 존재하지 않음
- 디렉터리를 변경하고 이 새 디렉터리에 상대적인 파일명을 사용하는 것이 절대적인 파일명을 사용하는 것보다 효율적입니다.
```c
fd1 = open(“/usr/ben/abc”, O_RDONLY);
fd2 = open(“/usr/ben/xyz”, O_RDWR);
```
```c
chdir(“/usr/ben”);
fd1 = open(“abc”, O_RDONLY);
fd2 = open(“xyz”, O_RDWR);
```
명령어 cd와 비슷한 시스템 콜, 프로세스의 워킹 디렉터리를 바꿔준다(절대, 상대 패스네임)  
해당 시스템 콜을 적용한 프로세스에만 적용(셸과 무관)  
오픈할 때 절대 경로명을 주면 길어서 귀찮은데, 체인지 디렉터리로 상대 경로명을 주면 편하다.

## The getcwd(3)
```c
#include <unistd.h>

char *getcwd(char *name, size_t size);

//Returns: name if OK, NULL on error
```
- 현재 디렉터리 경로 이름에 대한 포인터를 반환합니다.
- 현재 디렉터리 이름은 `name`이 가리키는 배열에 복사됩니다.
- `name` 할당 문제
    - 구현은 시스템에 따라 다릅니다.
    - POSIX.1은 PATH_MAX 값(`<limits.h>`)을 지원하려 합니다.
    - 할당된 공간이 너무 작으면 오류가 반환되고 errno가 **ERANGE**로 설정됩니다.

\$ pwd 명령으로 현재 워킹 디렉터리를 볼 수 있는데 `getcwd` 라이브러리가 이와 유사하다.  
`<unistd.h>`를 인클루드하고,  
char *getcwd(char *name, size_t size); 성공하면 네임을 리턴, 실패하면 널을 리턴한다.  
첫 번째 인자가 리턴된다, 두 번째 인자는 최대 사이즈다.

현재 디렉터리의 패스네임을 포인터로 넘겨준다.  
그걸 저 첫 번째 인자에도 넘겨준다(양 쪽으로).  
문제는 시스템마다 버전이 조금씩 다르다, POSIX.1은 PATH_MAX 값을 지원하려 한다.

### example
```c
/* my_pwd -- 작업 디렉터리를 프린트한다.*/

#include <stdio.h>
#include <unistd.h>
#define VERYBIG 200

void my_pwd (void);

main()
{
   my_pwd();
}

void my_pwd (void)
{
   char dirname[VERYBIG];

   if ( getcwd(dirname, VERYBIG) == NULL)
 	perror("getcwd error");
   else
 	printf("%s\n", dirname);
}
```
메인에서 my_pwd 함수 실행, 캐릭터형 dirname 크기를 충분히 잡고 getcwd에 인자로 준다.  
경로명을 출력하는 간단한 프로그램

## The ftw(3)
### The ftw(3) (1/2)
```c
#include <unistd.h>

int ftw(const char *path, int (*func)(), int depth);

//Returns: 0 if OK, -1 on error
```
- 임의의 디렉터리에서 시작하여 디렉터리 트리 탐색을 수행하고, 발견된 각 디렉터리 항목에 대해 사용자 정의 루틴을 호출합니다.
- ftw의 종료 조건
    - 사용자 정의 함수가 0이 아닌 값을 반환할 때
    - 오류가 발생했을 때
    - 트리의 맨 아래에 도달했을 때
- 인자(Argument)
    - path : 디렉터리 경로 이름
    - depth : 다른 파일 서술자의 수를 제어함.
    - func : 사용자 정의 함수

### The ftw(3) (2/2)
```c
int func(const char *name, const struct stat *sptr, int type)
{
	/* body of function */
}
```
- 인자(argument)
    - name : 객체 이름
    - sptr : 객체에 대한 stat 구조
    - type <ftw.h>
        - FTW_F 파일
        - FTW_D 디렉터리
        - FTW_DNR 읽을 수 없는 디렉터리.
        - FTW_SL 심볼릭 링크.
        - FTW_NS 심볼릭 링크가 아니며, stat를 성공적으로 실행할 수 없음.(stat(2) 오류)

### 예제
```c
#include <sys/stat.h>
#include <ftw.h>

int list(const char *name, const struct stat *status, int type)
{
   if (type == FTW_NS) /* 만일 stat 호출이 실패하면, 그냥 복귀한다. */
      return 0;
 /* 아니면 객체이름, 허가 그리고 객체가 디렉터리이거나 상징형 링크면 뒤에 “*”를 첨가*/
   if(type == FTW_F)
      printf("%-30s\t0%3o\n", name, status->st_mode&0777);
   else
      printf("%-30s*\t0%3o\n", name, status->st_mode&0777);

   return 0;
}

int main (int argc, char **argv){
   int list(const char *, const struct stat *, int);

   if (argc == 1)
      ftw (".", list, 1);
   else
      ftw (argv[1], list, 1);
   exit (0);
}
```
```c
$ list
.			*   	0755
./list		        *  	0755
./file			        0644
./subdir		*	0777
./subdir/another		0644
./subdir/subdir2	*	0755
./subdir/yetanother		0644
```
이 부분은 생략합니다.

# 4.5 UNIX file systems
## Caching sync and fsync
- 유닉스 파일 시스템에서 유용한 명령어
- 메모리에서 디스크로의 모든 전송, 즉 쓰기는 일반적으로 디스크에 즉시 기록되는 것 대신 운영 체제의 데이터 공간에 캐시됩니다.
- 읽기도 캐시 내에 버퍼링됩니다.
- UNIX는 버퍼의 데이터를 디스크에 쓰는 두 가지 함수를 제공합니다.
- sync
    - 파일 시스템에 대한 정보가 포함된 모든 주 메모리 버퍼를 디스크에 플러시(flush, 물내리기)하는 데 사용됩니다.
- fsync
    - 특정 파일과 관련된 모든 데이터와 속성을 플러시하는 데 호출됩니다.

프로그램을 실행하면 디스크 메모리에 업데이트가 된다,  
라이트 시스템 콜을 써서 I/O하다, 메모리 버퍼에 디스크에 라이트하지 않은 것들이 남아있는 경우가 종종 있다.  
메모리 버퍼뿐 아니라 OS 커널 버퍼도 있는데, 시스템 실행 중에 전원이 나가거나 갑자기 종료되면 작업한 것들이 날아간다.  
리드도 마찬가지로 캐시가 버퍼된다.

그래서 주기적으로 flush, flush out 즉, 버퍼에 있는 내용을 다 디스크에 write시킨다.  
sync는 파일 시스템 전체에 대해 메인 메모리 버퍼에 남아있는 내용을 디스크에 write한다.  
fsync는 특정한 파일 하나 하나(파일 디스크립터가 가리키는) 버퍼에 있는 내용을 디스크에 write시켜준다.

wirte와 같은 메모리로부터 디스크로의 모든 전송은 일반적으로 디스크에 즉시 write되는 대신에 operating system’s data space(메모리)에 캐쉬(cache)된다.  
read도 마찬가지로 cache에 buffered된다.  
유닉스는 cached된 데이터를 disk로 write하기 위해 두개의 system call을 제공한다, sync와 fsync이다.  
sysnc는 모든 파일시스템에 있는 buffere된 내용을 disk로 write한다.   
fsync는 특별한 파일과 관련된 모든 데이터와 에트리뷰트들을 flush한다.

## The sync(2) & fsync(2) system call
```c
#include <unistd.h>

int fsync(int filedes);
 
//Returns: 0 if OK, -1 on error
void sync(void);
```
- 둘의 중요한 차이점
    - fsync는 모든 파일 데이터가 디스크에 쓰여질 때까지 반환되지 않습니다.
    - sync 호출은 데이터 쓰기가 예약되었지만, 완료되지 않은 경우에도 반환될 수 있습니다.
        - 단지 운영체제에 스케줄을 요청한다, 따라서 바로 리턴된다.
- UNIX 시스템은 `sync`를 반복적으로 호출하는 코드를 지속적으로 실행합니다.

둘 다 시스템 콜, `<unistd.h>` 인클루드하고,  
int fsync(int filedes); void sync(void); 리턴 값은 성공 시 0, 실패 시 –1  
싱크는 인자 없음, 에프싱크는 파일 디스크립터 값을 줌  

에프싱크는 특정 파일에 대해서만 하는 것이기에 디스크에 모든 데이터가 플러시 아웃(flush out)될 때까지 리턴이 안 된다.  
싱크는 즉각 리턴된다(모든 데이터 write이 스케쥴되고 완료되지 않아도).  
유닉스 시스템은 OS 차원에서 싱크를 주기적으로 실행시켜준다.

# 4.6 UNIX device files
유닉스의 디바이스 파일들(디스크, 테이프, 키보드, USB, 모니터, 프린터 등등, 디스크도 여려 개 있음)

## Device of UNIX
### Device of UNIX (1/2)
UNIX에서 장치(Device)는 할당된 특정한한 장치 번호(Device number)로 액세스합니다.  
장치 번호는 major-number와 minor-number로 구성됩니다.  
major-number: 장치 유형(type of device, 장치 드라이버(device driver))에 따라 정해짐.  
minor-number: 특정 장치의 인스턴스.(같은 종류의 디바이스가 여러 종류 있으니 구분하기 위한 번호)

하지만 사용자들은 특정 장치에 액세스하는 데 장치 번호를 사용하고 싶어하지 않습니다.(각 디바이스에 대한 숫자의 쌍을 기억하길 원하지 않는다.)  
이에 대한 자연스러운 해결책은 파일 뿐 아니라 디바이스를 기술하기 위해서도 파일시스템 이름 공간을 사용하는 것입니다.  
UNIX는 디바이스 파일(device file)이라는 개념을 도입하여 장치 번호를 장치 파일에 매핑했습니다.(파일과 디바이스에 대한 일관된 인터페이스를 제공함)  
파일을 특정 디바이스에 넣기 위해선 메이저와 마이너 넘버 둘을 동시에 지정해야 합니다.

유닉스 파일시스템의 특징  
- 파일이 디렉터리나 심볼릭 링크, 디스크나 터미널, 프린터 같은 하드웨어 장치들, 시스템 메모리 같은 가상 장치, 그리고 파이프나 소켓 같은 통신 추상 개념 등 입/출력과 관련된 모든 종류의 객체들을 포함하도록 파일 추상 개념을 일반화 하고 있음
- 이들은 파일 디스트립터를 통해 접근 가능
- 기존 일반파일의 시스템 콜을 통해 이러한 특수 파일들을 조작한다.

### Device of UNIX (2/2)
- 장치 파일(Device file, `/dev/`에 있음, 루트 밑 dev 디렉터리 안에 장치 파일들 모여 있음)
- 장치 파일의 i-노드에는 장치 번호(inode->i_rdev)가 포함됩니다.
    - st_dev(해당 장치 파일이 저장되어있는 디스크의 메이저, 마이너 넘버가 있는 곳)
    - st_rdev(메이저, 마이너 넘버가 있는 곳)
- 주변 장치(peripheral devices)에 대한 간단한 인터페이스를 제공합니다.
- 장치 파일은 파일 시스템에 나타납니다.
- 사용자는 표준 시스템 호출을 사용하여 장치에 액세스하고 이를 일반 컴퓨터 파일처럼 취급합니다.
    - 일반 파일을 디바이스에 바로 아웃풋할 수 있다.
- 장치 파일은 슈퍼유저만 생성 가능합니다.

주변 장치(peripheral device)  
유닉스 시스템에서 주변 장치는 파일 시스템의 파일 이름을 통해 액세스(접근)합니다.  
디스크, 터미널, 프린터, 테이프 장치 등이 있습니다.  
일반적인 디스크 파일과 달리 이러한 장치 파일에 대한 읽기 및 쓰기는 시스템과 해당 주변 장치 간에 데이터를 직접 전송합니다.(즉, 캐쉬를 거치지 않습니다.)  

- 이러한 특수 파일들은 `/dev`라는 디렉터리에 저장됩니다.
    - /dev/tty00    (터미널)
    - /dev/console  (콘솔)
    - /dev/pts/as   (터미널)
    - /dev/lp   (라인 프린터)
    - /dev/rmt0     (마그네틱 테이프)
    - /dev/rmt/0cbn     (마그네틱 테이프)
    - /dev/dsk/c0b0t0d0s3   (디스크)
    - /dev/dsk/hd0d     (디스크)

장치 파일은 command level 또는 일반 파일처럼 프로그램 내에서 사용할 수 있습니다.
```
 $ cat fred > /dev/lp
 $ cat fred > /dev/rmt0
```
```
 mknod(path, mode, dev);
```
- path : dev번호를 맵핑할 파일명
- mode : 접근 권한
- dev : major+minor번호
- 디바이스 파일의 장점
    - 디바이스에 대한 접근을 일관된 방법으로 컨트롤 할 수 있음.
    - 파일 입출력 루틴을 디바이스에 그대로 적용이 가능

### example
```c
#include<fcntl.h>

main()
{
   int i, fd;

   fd = open("/dev/tty00", O_WRONLY);

   for(i = 0; i < 100; i++)
      write(fd, "x", 1);

   close(fd);
}
```
터미널 디바이스는 멍텅구리다.  
라이트 온리로 오픈하고, 리얼 모니터를 가리키는 포인터를 리턴한다.  
모니터에 x를 100번 라이트하면 출력한다.

## Block and character device files
### Block and character device files (1/4)
블록 장치 파일(Block device file)  
I/O 단위가 블록 단위(한 번 이동 시 4k 바이트 단위) 전송  
디스크, 자기 테이프등이 있다.  
랜덤 액세스 가능(테이프는 시퀀셜 엑세스)  
파일 시스템은 블록 장치에만 존재할 수 있으므로 이러한 블록 장치는 빠른 액세스를 위해 종종 원시 장치라고 알려진 연관된 문자 장치를 갖습니다. (mkfs, fsck)

문자 장치 파일(Character device file)  
터미널, 모뎀, 프린터, 키보드 등  
랜덤 액세스(가능할 수도 있지만 대게는 불가능)

UNIX는 주변 장치(I/O 기능들)와 상호 작용하기 위해 두 개의 운영 체제 구성 테이블을 사용합니다.  
- 블록 장치 스위치 테이블: bdevsw[]
- 문자 장치 스위치 테이블: cdevsw[]

두 테이블 모두 장치 파일의 i-노드에 저장된 주요 장치 번호라는 값을 사용하여 인덱싱됩니다.

**실질적으로 단일 인터페이스가 모든 디바이스들에 적합하지는 않다**  
- 왜냐하면 디바이스들의 기능과 접근 방법이 매우 다양하기 때문이다.
- 그래서 유닉스는 두 종류의 디바이스로 구분하고, 각각에 대해 다른 인터페이스를 정의한다.

**Block device**  
- 블록 단위로 데이터를 저장하고 입출력을 수행한다.
- 블록의 크기는 512 혹은 ‘512 X 2의배수’ 이다.
- 하드디스크, 플로피디스크, CD-ROM
- 블록 디바이스만이 유닉스 파일 시스템을 포함할 수 있다.
- 커널은 buf구조를 통해 블록 디바이스 드라이버들과 통신한다. 즉, buf를 통해 입출력된다.
- 전통적으로 buffer cache라 불리는 메모리 장소에 데이터를 쓰거나 그 메모리 장소로 부터 데이터를 읽어옴

**Character device**  
- 임의의 크기의 데이터를 저장하거나 전송할 수 있음
- 커널은 데이터를 순차적으로 접근하는 연속된 바이트-스트림으로 해석
- 임의의 주소로 접근 불가능, 탐색 연산을 허용하지 않음
- 터미널, 프린터, 마우스, 사운드 카드등.

**디바이스 드라이버**  
- 커널은 디바이스 드라이버를 통해 장치와 상호 작용한다.
- 드라이버는 하나 이상의 장치를 제어
- 디바이스와 상호 작용하는 유일한 모듈.
- 디바이스 드라이버는 struct cdevsw나 bdevsw의 함수들에 대한 특정한 구현을 제공해야 한다.(함수 포인터로부터 디바이스에 종속적인 함수로 dereferencing함)

### Block and character device files (2/4)
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG029.jpg "그림08"){: .align-center}
- 주변 장치로 데이터를 전송하거나 주변 장치에서 데이터를 전송하는 순서는 다음과 같다.
    - 읽기 또는 쓰기 시스템 콜은 일반적으로 장치 파일의 i-노드에 액세스합니다.
    - 시스템은 i-노드 구조 내의 플래그를 확인하여 장치가 블록 장치인지 문자 장치인지 확인합니다. 메이저 번호도 추출됩니다. **->** major(inode->i_rdev)
    - 메이저 번호는 적절한 장치 구성 표(device configuration table)에 대한 인덱싱에 사용되고 장치별 드라이버 루틴이 호출되어 데이터 전송을 수행합니다. **->**(*(cdevsw[major(dev)].d_write))(dev, … )

마이너 장치 번호도 i-노드에 저장되고 장치 드라이버 루틴에 전달되어 여러 주변 포트를 지원하는 장치에서 액세스되는 포트를 정확히 식별합니다.

* 커널은 디바이스를 명명하기 위해 번호를 붙인다.
* 사용자는 디바이스를 명명하기 위해 pathname을사용(디바이스 파일), 이로 인해 디바이스를 일반 파일과 같이 취급할 수 있음.
* 입출력 서브 시스템은 커널과 사용자 이름 공간사이의 의미를 정의하며, 그들 사이의 매핑을 수행.
* 커널은 디바이스 유형(블록, 문자)과 주, 부 디바이스 번호라 불리는 한 쌍의 번호에 의해 각 디바이스를 식별
* Major device number
    - device 의 종류, 즉, 드라이버를 나타낸다.
* minor device number
    - 디바이스의 특정한 인스턴스를 나타낸다. 
* 블록과 문자 디바이스는 그들 자신의 독립적인 주 번호들의 집합을 가짐. 따라서 블록 디바이스에 대한 주 번호 5는 디스크 드라이버를 가리킬 수 있고 문자 디바이스에 대한 주 번호 5는 라인 프린터를 가리킬 수 있다.
* 사용자를 위한 디바이스 파일은 수퍼유저만이 생성할 수 있다. mknod임.
>mknod(path, mode, dev)

### Block and character device files (3/4)
```c
struct stat {
       mode_t    st_mode;    /* file type & mode (permissions) */
       ino_t     st_ino;     /* i-node number (serial number) */
       dev_t     st_dev;     /* device number (file system) */
       dev_t     st_rdev;    /* device number for special files */
       nlink_t   st_nlink;   /* number of links */
       uid_t     st_uid;     /* user ID of owner */
       gid_t     st_gid;     /* group ID of owner */
       off_t     st_size;    /* size in bytes, for regular files */
       time_t    st_atime;   /* time of last access */
       time_t    st_mtime;   /* time of last modification */
       time_t    st_ctime;   /* time of last file status change */
       blksize_t st_blksize; /* best I/O block size */
       blkcnt_t  st_blocks;  /* number of disk blocks allocated */
     };
```
이전에 봤던 stat 구조체  
stat 시스템 콜로 파일 네임, 스트럭트 스탓 포인터 해서 아이노드 정보들을 가져오는데,  
st_mode는 퍼미션 정보(4비트 파일 타입 3비트 sgt, 유저,그룹,아더스 퍼미션)  
st_ino 아이노드 넘버  
st_rdev 리얼 디바이스의 메이저, 마이너 넘버  
st_dev 디바이스 파일이 저장되어 있는 디스크의 메이저, 마이너 넘버

### Block and character device files (4/4)
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG030.jpg "그림09"){: .align-center}
참고, 메이저(장치 종류)와 마이너(동일 장치별 개별 번호) 넘버가 필요한 이유  
유닉스는 각 디바이스가 오퍼레이션(연산)들을 구현하도록 요구하고 있다.  
이러한 연산들은 struct cdevsw[] 이나 struct bdevsw[]에 캡슐화되어 있다.  
Struct cdevsw나 struct bdevsw의 각 필드는 추상화된 디바이스에 대한 인터페이스를 가지고 있다.  
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG031.jpg "그림10"){: .align-center}
예제, 한 번 볼 것

## The stat structure revisited
### The stat structure revisited (1/3)
**st_mode**  
- 해당 파일 이름과 해당 i-노드를 포함하는 파일 시스템의 장치 번호입니다.
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG032.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG033.jpg "그림12"){: .align-center}
```
$ ls -lL /dev/tty[01] /dev/hda[34]
brw-------  1 root   3, 3 Dec 31  1969 /dev/hda3
brw-------  1 root   3, 4 Dec 31  1969 /dev/hda4
crw-------  1 root   4, 0 Dec 31  1969 /dev/tty0
crw-------  1 root   4, 1 Jan 18 15:36 /dev/tty1
```
스탓 다시 보기  
st_mode 퍼미션 총 16비트(2바이트)  
파일 타입은 총 7가지, 비트별 파일 타입을 알아보기 힘드니 매크로를 준비해 놓음  
if(S_ISREG(sb.st_mode)) 이게 트루면 레귤러 타입 파일  
이즈 캐릭터(S_ISCHR) – 캐릭터 디바이스 파일이면 트루 아니면 펄스  
나머지도 마찬가지

### The stat structure revisited (2/3)
**struct stat**
- st_mode
![그림13](https://ji-hun-park.github.io/assets/images/LNXIMG034.jpg "그림13"){: .align-center}
```c
switch(stat->st_mode & S_IFMT){
    case S_IFBLK:
    case S_IFCHR:
    case S_IFIFO:
    case S_IFREG:
    case S_IFDIR:
    case S_IFLNK:
    case S_IFSOCK:
}
```
- S_IFMT Type of file.
    - S_IFBLK - Block special. 
    - S_IFCHR - Character special.
    - S_IFIFO - FIFO special. 
    - S_IFREG - Regular. 
    - S_IFDIR - Directory. 
    - S_IFLNK - Symbolic link. 
    - S_IFSOCK - Socket.

또 다른 방법으로 아이노드 값을 스탓으로 가져와서 stat.h에 정의되어 있는 값들을 이용한다.  
S_IFMT와 비트와이즈앤드 연산으로 앞 4비트만 가져온다.  
각각의 케이스로 구분할 수 있다.

### The stat structure revisited (3/3)
- st_rdev
    - 문자 특수 파일과 블록 특수 파일만 `st_rdev` 값을 갖습니다.
    - 매크로 major와 minor는 대부분 구현(implementations)에서 정의됩니다. (`<sys/types.h>`)

```c
int main(int argc, char *argv[]) {
   int i; 
   struct stat buf; 
   stat(argv[1], &buf);

   printf("dev = %d/%d", major(buf.st_dev), minor(buf.st_dev)); 

   if (S_ISCHR(buf.st_mode) || S_ISBLK(buf.st_mode)) { 
      printf(" (%s) rdev = %d/%d",(S_ISCHR(buf.st_mode))?"character":"block" 
                                 , major(buf.st_rdev)
                                 , minor(buf.st_rdev)); 
   } 
   printf("\n"); 
   exit(0); 
}
```
리얼 디아비스 메이저, 마이너 넘버가 있는 곳.  
오직 캐릭터, 블록 스폐셜 파일만이 해당 값을 가진다.  
시스템 안 타입스(types) 헤더를 인클루드하고,  
메이저와 마이너 위치를 찾아서 번호를 가져오는 매크로가 있다.

buf에 아이노드 정보가 들어가고,  
그 값을 메이저와 마이너에 넣으면 해당 장치의 파일이 들어있는 디스크의 대한 메이저, 마이너 넘버가 나온다.

이제 이즈캐릭터와 이즈블록으로 디바이스 파일인지 판별한다.  
디바이스 파일에만 있는 rdev 값으로 캐릭터가 트루면 캐릭터, 펄스면 블록이다.  
이후 rdev를 메이저와 마이너에 넣어서 값을 알아낸다.

## File system information
### File system information (1/2)
```c
#include <sys/statvfs.h>

int fstatvfs(int fildes, struct statvfs *buf);
int statvfs(const char *path, struct statvfs *buf);

//Returns: 0 if OK, -1 on error
```
- **statvfs**와 **fstatvfs**를 호출하면 총 여유 디스크 블록 수, 여유 i-노드 수와 같은 기본적인 파일 시스템 정보를 얻을 수 있습니다.

```c
struct statvfs {
    unsigned long f_bsize    	File system block size. 
    unsigned long f_frsize   	Fundamental file system block size. 
    fsblkcnt_t    f_blocks	Total number of blocks on file system in units of f_frsize. 
    fsblkcnt_t    f_bfree    	Total number of free blocks. 
    fsblkcnt_t    f_bavail   	Number of free blocks available to non-privileged process. 
    fsfilcnt_t    f_files    	Total number of file serial numbers. (#inodes)
    fsfilcnt_t    f_ffree    	Total number of free file serial numbers. 
    fsfilcnt_t    f_favail   	Number of file serial numbers available to non-privileged process. 
    unsigned long f_fsid     	File system ID. 
    unsigned long f_flag     	Bit mask of f_flag values. 
    unsigned long f_namemax  	Maximum filename length.      
}
```
시스템 에러 정보(생략)

### File system information (2/2)
**f_flag**  
![그림14](https://ji-hun-park.github.io/assets/images/LNXIMG035.jpg "그림14"){: .align-center}
- f_bsize
    - 파일 시스템 블록 크기(바이트). 이 숫자는 디스크 단위 스토리지 블록의 바이트 수입니다.
- f_frsize
    - 디스크 주소가 계산되는 단위입니다. UFS의 경우 512 또는 1K 바이트가 될 수 있습니다.

파일 시스템 에러 정보(생략)

### example
```c
/* fsys -- 파일 시스템 정보를 프린트한다. */
/* 파일 시스템 이름이 인자로 전달된다. */
#include <sys/statvfs.h>
#include <stdlib.h>
#include <stdio.h>

main (int argc, char **argv)
{
   struct statvfs buf;

   if (argc != 2)
   {
      fprintf (stderr, "usage: fsys filename\n");
      exit (1);
   }
   if (statvfs (argv[1], &buf) !=0)
   {
      fprintf (stderr, "statvfs error\n");
      exit (2);
   }
   
   printf ("%s:\tfree blocks %d\tfree inodes %d\n",
 		     argv[1], buf.f_bfree, buf.f_ffree);
   exit (0);
}
```
사용 예제 정보(스탓이랑 비슷하다, 생략)

## Limits
### Limits (1/4)
- 두 가지 유형의 제한이 필요합니다.
    - 컴파일 타임 제한(예: 짧은 정수의 가장 큰 값은?)
    - 런타임 제한(예: 파일 이름의 문자 수는?)

- 이러한 문제를 해결하기 위해 세 가지 유형의 제한이 제공됩니다.
    - 컴파일 타임 제한(헤더)
    - 파일이나 디렉터리와 연관되지 않은 런타임 제한(sysconf 함수)
    - 파일이나 디렉터리와 연관된 런타임 제한(pathconf 및 fpathconf 함수)

### Limits (2/4)
```c
#include <unistd.h>

long sysconf(int name);
long pathconf(const char *pathname, int name);
long fpathconf(int filedes, int name);
 
//All three return: corresponding value if OK, -1 on error (see later)
```
![그림15](https://ji-hun-park.github.io/assets/images/LNXIMG036.jpg "그림15"){: .align-center}
`_SC_`로 시작하는 상수는 **sysconf**의 인자로 사용됩니다.  
`_PC_`로 시작하는 상수는 **pathconf** 및 **fpathconf**의 인자로 사용됩니다.

- Limit이 결정 안되는 경우, 
    - sysconf()는 -1을 리턴.
    - 하지만, errno=0임
- Limitm 결정이 안되는 상황
    - `<limits.h>`정의가 안되어 있을 경우(compile-time에 결정이 안됨)
    - runtime에 결정 안될 수도 있다.

### example
```c
#include <unistd.h>
#include <stdio.h>

typedef struct{
   int val;
   char *name;
} Table;

main()
{
   Table *tb;
   static Table options[] = {
 	{ _PC_LINK_MAX, "Maximum number of links"},
 	{ _PC_NAME_MAX, "Maximum length of a filename"},
 	{ _PC_PATH_MAX, "Maximum length of pathname"},
 	{-1, NULL}
   };

   for (tb=options; tb->name != NULL; tb++)
      printf ("%-28.28s\t%ld\n", tb->name, pathconf ("/tmp", tb->val));
}
```
```
Maximum number of links		32767
Maximum length of a filename	256
Maximum length of a pathname	1024
```
생략

### Limits (3/4)
**sysconf 시스템 콜**  
- 런타임에 다음 두 가지를 확인합니다.
    - 시스템 전체 옵션 확인
    - 구현에 따른 제한 확인
- 일반적으로 구현 시스템에 대한 정보를 얻는 데 사용됩니다.
- -1 반환(return)
    - errno = **EINVAL**: 잘못된 심볼
    - errno가 변경되지 않음
        - 옵션이 지원되지 않음(옵션 확인)
        - 제한이 없음(제한 확인)
    - errno가 변경된 경우에만 -1은 오류를 의미합니다.(에러가 안나더라도, limit을 결정할 수 없을때 errno는 설정되지 않는다.)  
    - <span style="color:red">따라서 호출하기 전에 errno를 0으로 설정해야 합니다.</span>

### Limits (4/4)
- fpathconf와 pathconf
    - 파일에 대한 정보를 얻는 데 사용됩니다.
    - fathconf는 열린 파일이 있을 때 사용됩니다.
    - pathconf는 닫힌 파일이 있을 때 사용됩니다.
    - 반환 값의 해석은 sysconf와 동일합니다.

## 마무리
이상으로 Linux 이론의 디렉터리편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
