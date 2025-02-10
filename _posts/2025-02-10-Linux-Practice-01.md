---
title:  "Linux 기초 실습 01 Ubuntu 환경 설정"
excerpt: "Linux 기초 실습 01 환경 설정하기"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Practice
last_modified_at: 2025-02-10T16:00:00-00:00
---

# Linux Lecture Practice 01 Ubuntu Environment Setting

## Introduction
본래 여기서 실습 환경 설정에 관한 설명을 진행해야하나,  
여러 우분투 환경을 고려해 그에 대한 설명은 생략합니다.  
각자의 방법으로 Ubuntu 18.04를 세팅하시면 됩니다.  
```
tip: GUI가 제공될 경우, 단축키 [CTRL]+[ALT]+T 를 통해 터미널 창을 띄운 후, 터미널 아이콘에 커서를 대고 우클릭 후 “즐겨찾기에 추가”를 클릭하면 터미널 실행이 편해집니다.
```
## Packages
### 1. Build-essential 설치하기
프로그램을 개발하는데 필요한 library를 제공하는 유틸리티입니다. 터미널 실행 후 셸에 다음을 입력  
```c
sudo apt-get install build-essential
// sudo 실행 시 처음에 설정한 비밀번호를 입력해야 합니다.
```

### 2. vim editor 설치하기
텍스트 편집기입니다. 터미널 실행 후 셸에 다음을 입력  
```c
sudo apt-get install vim
```
> vim이 익숙지 않으신 분들은 GNU **Nano** Editor로 코드 작성하셔도 됩니다.

### 3. ctags 설치하기
ctags는 소스 파일을 태그 단위로 잘라서 태그 정보를 토대로 소스 분석을 도와주는 유틸리티입니다. 터미널 실행 후 셸에 다음을 입력  
```c
sudo apt-get install ctags
```

### 4. gdb 설치하기
디버깅 기능을 제공하는 유틸리티입니다. 터미널 실행 후 셸에 다음을 입력  
```c
sudo apt-get install gdb
```

## 마무리
이상으로 이번 실습을 마치겠습니다.
열심히 따라와 주셔서 감사합니다!
