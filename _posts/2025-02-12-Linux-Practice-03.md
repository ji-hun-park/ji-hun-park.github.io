---
title:  "Linux 기초 실습 03 VI Editor"
excerpt: "Linux 기초 실습 03 VI Editor 명령어 모음"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Practice
last_modified_at: 2025-02-12T18:00:00-00:00
---

# Linux Lecture Practice 03 VI Editor

## Introduction
이번 실습에서는 VI Editor의 사용법에 대해 알아보겠습니다.

## VI 에디터란?
**Visual Display Editor**  
- 리눅스 환경에서 사용되는 가장 대표적인 편집기
- 한 줄씩 편집하는 줄 단위 편집기가 아닌 한 화면을 편집하는 Visual Editor라는 의미
- 현재는 VI 에디터의 기능을 모방하고 기술 발전 양상을 반영한 `VIM`(**VI** i**m**proved)가 리눅스 배포판에 기본적으로 포함되어 있음
  - VIM 이외에도 VI의 변종(nVi, elVis)들이 존재

## VI 에디터 사용법
### 시작하기
- 터미널 화면에서 ‘vi [파일이름]’
```
 $ vi main.c
```
- 읽기 전용으로 볼 때는 –R 추가
```
 $ vi –R main.c
```

### VI 모드
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG001.jpg "그림01"){: .align-center}
- 일반 모드
  - 기본 모드
  - 화살표 이동이나 특정 문자의 수정/삭제 및 편집에 쓰이는 대부분의 명령어를 실행하는 모드
  - 입력, 명령 모드에서 ESC를 통해 일반 모드로 돌아올 수 있음

- 입력 모드
  - 일반 모드에서 **i(기본)**, a, o를 입력하면 입력 모드로 전환되어 텍스트 입력이 가능

- 명령 모드
  - 일반 모드에서 콜론(‘:’, Shift+;)을 입력하여 전환, 명령모드로 들어가면 화면 하단 버퍼에서 커서가 깜빡이는 상태로 전환됨

## 옛날 키보드 배치
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG002.jpg "그림02"){: .align-center}

## 일반 ▶ 입력 모드 전환
- **i(insert) – 커서 앞에 삽입**
- **a(append) – 커서 뒤에 삽입**
- o – 현재 줄 다음 줄에 삽입. 한 줄 생성
- O – 현재 줄 앞 줄에 삽입. 한 줄 생성
- A – 줄 끝에 삽입
- I – 줄 시작부분에 삽입

## 일반 ▶ 명령 모드 전환
- **:w – 파일 저장**
- :w abc.txt – abc.txt 파일로 저장
- **:w! – 파일 강제 저장 (! 붙일 시 강제로 이행)**
- **:q – 변경된 내용이 없을 때 그대로 종료 (+ !)**
- **:wq, :x – 저장 및 종료 (+ !)**
- :e abc.txt – abc.txt 파일 편집
- **:n – n번째 줄로 이동**
- :set nu – 행 번호 표시
- :set nonu – 행 번호 숨기기
- **:! – 잠시 쉘(터미널)로 복귀**
- :!abc – 터미널에서 abc 명령어 실행 후 복귀
- :sh – 쉘 프롬프트 실행(복귀 시 exit)

## 커서 이동
- h, j, k, l = ←, ↓, ↑, →
- $ - 라인 끝
- ^ - 라인 처음
- w – 다음 단어로 이동
- b – 앞 단어로 이동
- H – 화면 최상단
- M – 화면 가운데
- L – 화면 최하단
- e – 단어의 끝
- ‘n’G – ‘n’번째 라인 이동

## 화면 이동시 (Ctrl 키 사용)
- \+ F – 화면 단위로 앞(forward)으로 이동
- \+ B – 화면 단위로 뒤(backward)로 이동
- \+ D – 앞으로 반 화면 스크롤
- \+ U – 뒤로 반 화면 스크롤
- \+ L – 화면 재표시

## 삭제
- **x – 커서가 위치한 문자 삭제**
- ‘n’x – 커서가 위치한 문자부터 ‘n’번째 문자 삭제
- dw – 커서가 위치한 단어 삭제
- **dd – 커서가 위치한 줄 삭제**
- **‘n’dd – 커서가 위치한 라인부터 ‘n’번째 라인 삭제**
- **D – 커서가 위치한 부분부터 현재 줄 끝까지 삭제**
- dG – 커서가 위치한 줄에서 끝까지 삭제

## 변경 및 편집
- r – 커서가 위치한 문자를 다른 문자로 교체
- R – 커서가 위치한 부분부터 Esc 키를 누를 때까지 계속 덮어쓰기
- cw – 커서가 위치한 단어 변경
- cc – 커서가 위치한 줄 변경
- C – 커서가 위치한 곳 오른쪽 줄 변경
- s – 커서가 위치한 문자열 변경
- J – 현재 줄과 아래 줄 결합
- xp – 커서가 위치한 문자와 오른쪽 문자 교환
- ~ - 대/소문자 치환
- **u – 이전 명령 취소**
- . – 최근의 작업 반복 수행

## Copy
- **y – 문자 복사**
- yw – 커서가 위치한 단어 복사
- **yy – 커서가 위치한 줄 복사**
- yj – 커서가 위치한 줄과 그 다음 줄까지 복사
- yo – 커서가 위치한 줄의 처음까지 복사
- yk – 커서가 있는 줄과 그 앞 줄까지 복사
- **‘n’yy – n개의 라인 수만큼 복사**

## Paste
- **p – 삭제/복사된 내용을 커서가 있는 문자나 줄 뒤에 삽입**
- P – 삭제/복사된 내용을 커서가 있는 문자나 줄 앞에 삽입

## 검색
- /‘abc’ – 커서가 위치한 텍스트에서 순방향으로 ‘abc’ 검색
- ? ‘abc’ – 커서가 위치한 텍스트에서 역방향으로 ‘abc’ 검색
- n – 순방향으로 이전 검색 반복
- N – 반대 방향으로 이전 검색 반복

## Vi Editor 명령어 모음
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG003.jpg "그림03"){: .align-center}
```
set number    line 표시를 해줌
set ai    auto indent
set si    smart indent
set cindent   c style indent
set shiftwidth=4    shift를 4칸으로 ( >, >>, <, << 등의 명령어)
set tabstop=4   tab을 4칸으로
set ignorecase    검색시 대소문자 구별하지않음
set hlsearch    검색시 하이라이트(색상 강조)
set expandtab   tab 대신 띄어쓰기로
set background=dark   검정배경을 사용할 때, (이 색상에 맞춰 문법 하이라이트 색상이 달라짐)
set nocompatible    방향키로 이동가능
set fileencodings=utf-8,euc-kr    파일인코딩 형식 지정
set bs=indent,eol,start   backspace 키 사용 가능
set history=1000    명령어에 대한 히스토리를 1000개까지
set ruler   상태표시줄에 커서의 위치 표시
set nobackup    백업파일을 만들지 않음
set title   제목을 표시함
set showmatch   매칭되는 괄호를 보여줌
set nowrap    자동 줄바꿈 하지 않음
set wmnu    tab 자동완성 시 가능한 목록을 보여줌

syntax on   문법 하이라이트 킴
```
```
 vi ~/.vimrc
```

## 마무리
이상으로 이번 실습을 마치겠습니다.  
열심히 따라와 주셔서 감사합니다!
