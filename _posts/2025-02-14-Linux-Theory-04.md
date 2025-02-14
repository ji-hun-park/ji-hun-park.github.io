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
- cat은 파일들을 합쳐서 복사하는 명령(한 개면 단순 카피해서 옮기기)
    - 위 이미지에서 파란색 네모 안에 cat과 > 사이에 KBD가 빠짐
    - cat f1 f2 fn > fout
    - cat f1 > fout
- cat > out 이 경우엔 키보드가 된다
- ^D(컨트롤D)는 엔드 오브 파일(EOF), 입력이 끝나면 테스트1이라는 파일이 새로 생성된다.
- mkdir로 디렉 생성

디렉토리의 링크카운트는 처음생성시 무조건 2가된다. 이유는 디렉토를 생성한 위치에서의 디렉토리 엔트리와 자기 자신을 가리키는 .에 의해서이다. 그리고 새로 생성된 디렉토리는 ..이 있기 때문에 상위 디렉토리의 링크카운트를 1늘린다.

## 작성중
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG028.jpg "그림07"){: .align-center}
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
