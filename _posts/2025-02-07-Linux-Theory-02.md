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
    + 이전에 IBM은 ISAM, RF, VSAM등 여러 포맷이 있어 엑세스하는 API가 다 달랐습니다.(리드, 라이트 방법)
        - OS가 다 정해줬었습니다.
        - OS차원에서만 걷어냈을 뿐 해당 포맷들의 기능들은 사용 가능합니다.
* 각 바이트는 디스크 파일에서 개별적으로 주소 지정 가능합니다.(Addressable)
* 파일은 또한 외부 장치를 포함해 균일한(Uniform) 인터페이스입니다.(read, write)

### Filesystem
* 컴퓨터 파일과 데이터를 저장하고 구성(Organizing)하는 방법.
    - 기존 미니 디스크는 하이라키컬한 구조가 없었습니다.
* 파일을 쉽게 찾고 액세스할 수 있도록 합니다.
* 파일 시스템은 데이터 저장 장치인 하드 디스크, CD-ROM, USB, 마그네틱 테이프 등을 사용할 수 있습니다.

# 2.1 UNIX file access primitives
## UNIX primitives
![그림01](https://ji-hun-park.github.io/assets/images/그림24.jpg "그림01"){: .align-center}
+ 이 함수는 ANSI C의 일부가 아니지만 POSIX.1 및 XPG3의 일부입니다.
+ 이 장에서 설명하는 함수는 종종 버퍼링되지 않은 I/O라고 합니다.
+ 버퍼링되지 않은 I/O는 open, read, write, lseek 및 close 함수에서 제공합니다. 이러한 함수는 모두 파일 설명자와 함께 작동합니다.

![그림02](https://ji-hun-park.github.io/assets/images/그림25.jpg "그림02"){: .align-center}
![그림03](https://ji-hun-park.github.io/assets/images/그림26.jpg "그림03"){: .align-center}
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
