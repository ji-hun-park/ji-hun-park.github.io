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
### Users and ownerships (2/3)
### Users and ownerships (3/3)
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
![그림01](https://ji-hun-park.github.io/assets/images/그림46.jpg "그림01"){: .align-center}
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
