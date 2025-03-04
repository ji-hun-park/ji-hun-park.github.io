---
title:  "Linux 기초 이론 08 PROCESS & SHELL"
excerpt: "Linux 기초 이론 여덟 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-03-04T24:00:00-00:00
---

# Linux Lecture Theory 08 PROCESS & SHELL
## Introduction
지난 장에 이어 프로세스와 셸의 대해 설명하겠습니다.

# 5.9 smallsh: a command processor
## Basic logic of command processor
```
while(EOF not typed)
{

	Get command line from user  <- userin()

	Assemble command args and execute <- procline()

         Wait for child

}
```
셸의 처리과정은 다음과 같다.  
1. 사용자 입력을 받는다.
2. 명령을 실을 프로세스를 생성한다.(fork())
3. 새로 생성된 자식 프로세스는 입력 받은 문자열을 exec호출에 적합한 형태로 구성한다.
4. exec호출을 한다.

이 4단계를 계속적으로 반복 하는 것이 셸이다.

## 작성중
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG064.jpg "그림01"){: .align-center}
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG065.jpg "그림02"){: .align-center}
#### MAXBUF
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG066.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG067.jpg "그림04"){: .align-center}
[MAXBUF](https://ji-hun-park.github.io/linux/Linux-Theory-08/#maxbuf)
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG068.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG069.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG070.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG071.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG072.jpg "그림09"){: .align-center}

## 마무리
이상으로 Linux 이론의 프로세스 & 셸편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
