---
title:  "Linux 기초 실습 02 Basic Command"
excerpt: "Linux 기초 실습 02 기본 명령어 실습"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Practice
last_modified_at: 2025-02-10T18:00:00-00:00
---

# Linux Lecture Practice 02 Basic Command

## Introduction
이번 실습에서는 Unix 시스템에서의 로그인과 로그아웃,  
많이 사용하는 기본 명령어를 익혀보겠습니다.

## Prompt(프롬프트) 살펴보기
![그림01](https://ji-hun-park.github.io/assets/images/그림67.jpg "그림01"){: .align-center}
![그림02](https://ji-hun-park.github.io/assets/images/그림68.jpg "그림02"){: .align-center}

## 사용자 및 그룹 확인
- 사용자 확인 : id
  - 사용자의 ID와 그룹 ID까지 모두 조회
  - ID에는 고유의 번호도 포함
- 사용자 확인 : whoami
  - 사용자의 ID만 조회 (번호는 제외)
- 그룹 확인 : groups
  - 사용자가 소속된 그룹의 ID를 조회 (번호 제외)

![그림03](https://ji-hun-park.github.io/assets/images/그림69.jpg "그림03"){: .align-center}

## 연결 끊기
- Ctrl + C : Interrupt (인터럽트)
  - 실행중인 명령을 강제로 종료
  - 실제로는 인터럽트 시그널을 전송
- logout : 현재 사용자로서의 접속을 끊음
  - 로그인 셸에서만 사용
- exit : 상위 Shell로 탈출 (없으면 로그아웃)
  - 원격 작업에도 사용 가능
  - Ctrl + D로도 같은 기능을 함

## 디렉터리: 개요
- 웹과 동일한 구조(HTTP, FTP, …)
  - 루트(root)에서 시작하는 트리 구조
  - 디렉터리의 구분은 ‘/’ 기호를 이용
  - ‘/’ 자체는 루트를 의미
  - 모든 디렉터리는 루트에서 시작
  - 사용자는 일반적으로 ‘/home’에서 시작
- 예) /home/unix/system

### 경로
- 절대 경로 (Absolute Path)
  - 최상위 디렉토리부터 파일까지의 모든 디렉토리를 나열하여 파일의 위치를 표현
```c
 /home/unix/week_01.txt
```
- 상대 경로 (Relative Path)
  - 현재 위치(Current Working Directory : CWD)를 기준으로 파일의 위치를 표현

![그림00](https://ji-hun-park.github.io/assets/images/추가그림01.jpg "그림00"){: .align-center}

# Basic Command
## 명령행 구조
- 명령행(Commnad Line)에서 명령어와 옵션, 인자 입력
```
      프롬프트 명령어 [옵션] [인자]
       Ex) $ ls –la /usr/bin/a*
```
  - 옵션: 일반적으로 ‘-’뒤에 옴, 대부분의 명령어에 하나 이상의 옵션 사용가능
  - 인자: 명령어가 동작을 취할 대상(file, directory), **대소문자 구별**
  - 메타문자 사용가능
	  - ? : 1개의 임의의 문자
	  - * : 0개 이상의 임의의 문자 또는 문자열

## 디렉터리: pwd
- <span style="color:red">P</span>rint <span style="color:red">W</span>orking <span style="color:red">D</span>irectory
  - 현재 작업중인 디렉터리를 출력
- 사용법 : pwd (**PWD** <span style="color:red">아님!</span>)  
**모든 Unix는 대소문자 구분**
![그림04](https://ji-hun-park.github.io/assets/images/그림70.jpg "그림04"){: .align-center}

## 디렉터리: cd
- <span style="color:red">C</span>hange <span style="color:red">D</span>irectory
- 현재 작업중인 디렉터리를 변경

- 사용법 (반드시 뒤에 띄어쓰기 필요)
```
      $ cd [인자]
       여기서 인자는 directory 이름
```
- “cd” : 홈 디렉터리로 변경 ( = “cd ~”)
- “cd ..” : 상위 디렉터리
- “cd /” : 루트 디렉터리
- “cd /home/unix” : 절대경로
- “cd home/unix” : 상대경로
- “cd ~unix” : 특정 사용자의 홈 디렉터리
- “cd ~root” : root의 홈 디렉터리(= “cd /root”)

![그림05](https://ji-hun-park.github.io/assets/images/그림71.jpg "그림05"){: .align-center}

## 디렉터리: mkdir
- <span style="color:red">M</span>a<span style="color:red">k</span>e <span style="color:red">Dir</span>ectory
  - 현재 디렉터리 안에 디렉터리 생성

- 사용법 : mkdir 디렉터리명
```
 $ mkdir [옵션] 디렉터리
```
  - 디렉터리는 절대경로, 상대경로 모두 가능
  - 한 개 이상의 디렉터리 한번에 생성가능
  - 절대경로, 상대경로

- 옵션
  - -p : 지정된 디렉터리 중간의 디렉터리도 함께 생성
  - -m : 접근 권한을 부여해서 디렉터리 생성

![그림06](https://ji-hun-park.github.io/assets/images/그림72.jpg "그림06"){: .align-center}

- 디렉터리 구조를 모두 생성하려면
  - -p 옵션을 사용 (**P**arent)
- 사용법 : mkdir –p 생성할/상위/디렉터리

![그림07](https://ji-hun-park.github.io/assets/images/그림73.jpg "그림07"){: .align-center}

## 디렉터리: rmdir
- <span style="color:red">R</span>e<span style="color:red">m</span>ove <span style="color:red">Dir</span>ectory
  - 비어 있는 디렉터리를 제거
  - 디렉터리가 비어 있지 않으면 오류 발생
```
 $ rmdir [옵션] 디렉터리
```
- 옵션
  - -p : rmdir의 호출로 현재 디렉터리가 비게 되면 상위 디렉터리도 함께 삭제 시킨다.
  - 디렉터리는 해당 구조만 소유해야 하고, 다른 디렉터리가 있으면 오류 발생

![그림08](https://ji-hun-park.github.io/assets/images/그림74.jpg "그림08"){: .align-center}

## 디렉터리: dir & ls
- <span style="color:red">L</span>i<span style="color:red">s</span>t <span style="color:red">Dir</span>ectory Contents
  - 디렉터리 내의 파일 목록 표시

![그림09](https://ji-hun-park.github.io/assets/images/그림75.jpg "그림09"){: .align-center}
### 옵션 설정
- -a : **a**ll, 숨겨진 파일 표시
- -A : **A**lmost All, .과 ..을 표시하지 않음
- -l : **l**ong listing, 파일 1개 당 한 줄로 표시
- -s : 파일들이 차지하는 크기(**s**ize)를 block단위로 출력
- -R: 디렉터리의 내용과 서브 디렉터리의 내용을 재귀적(**R**ecursive)으로 출력
- -F : classi**f**y, 뒤에 형식을 표시
  - / : 디렉터리
  - @ : 심볼릭 링크 (바로가기)
![그림10](https://ji-hun-park.github.io/assets/images/그림76.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림77.jpg "그림11"){: .align-center}

### 인자 사용
- ls ttyS0 : ‘ttyS0’ 파일을 찾음
- ls t* : t로 시작하는 모든 파일을 찾음
- ls t*0 : t로 시작하고 0으로 끝나는 모든 파일
- ls t?? : t로 시작하고 나머지 2글자는 아무거나
  - 옳은 예 : ttt, tty, txt, try, …
  - 틀린 예 : ty, text, tell, …

![그림12](https://ji-hun-park.github.io/assets/images/그림78.jpg "그림12"){: .align-center}

### mode
![그림13](https://ji-hun-park.github.io/assets/images/그림79.jpg "그림13"){: .align-center}
- 모든 파일의 mode는 10자리로 구성
- 1번째 자리 : 특수 비트
  - \- : 일반 파일
  - b : 바이너리 장치
  - c : 문자열 장치
  - d : 디렉터리
  - l : 심볼릭 링크
- 2~10번째 자리 : 권한
  - 2~4 : 소유자 권한
  - 5~7 : 소유 그룹 권한
  - 8~10 : 그 외 권한
  - <span style="color:red">**R**</span>ead, <span style="color:red">**W**</span>rite, E<span style="color:red">**x**</span>ecute를 의미

## 파일: touch
- 빈 파일을 생성
  - touch (생성할 파일)
- 파일이 이미 존재하면 <ins>수정 시각을 업데이트</ins>

![그림14](https://ji-hun-park.github.io/assets/images/그림80.jpg "그림14"){: .align-center}

## 파일: cp
- <span style="color:red">C</span>o<span style="color:red">p</span>y file(s)
  - 파일을 복사
- 사용법: cp [옵션] (원본) (대상)
  - file1을 file2로 복사
```
 $ cp [옵션] file1 file2
```
  - File을 directory 내부로 복사(이름 보존)
```
 $ cp [옵션] file directory
```
  - directory1의 내용을 모두 directory2에 복사.
```
 $ cp –r [옵션] directory1 directory2
```
![그림15](https://ji-hun-park.github.io/assets/images/그림81.jpg "그림15"){: .align-center}

### 옵션
- -i : **I**nteractive, 덮어쓰기 여부를 물음
- -r : **R**ecursive, 파일 구조를 모두 복사 (디렉터리 등)

![그림16](https://ji-hun-park.github.io/assets/images/그림82.jpg "그림16"){: .align-center}

## 파일: mv
- <span style="color:red">M</span>o<span style="color:red">v</span>e file(s)
  - 파일을 이동
- 사용법: mv [옵션] (원본) (대상)
  - 파일명을 file1을 file2로 바꾼다. file2가 존재하면 덮어쓰기
```
 $ mv [옵션] file1 file2
```
  - file을 directory로 이동
```
 $ mv [옵션] file directory
```
  - 전체 디렉터리가 이동. 실제 파일이 이동하는 것이 아니라 계층적 구조에 따라 이름만 이동.
```
 $ mv [옵션] directory1 directory2
```
![그림17](https://ji-hun-park.github.io/assets/images/그림83.jpg "그림17"){: .align-center}
- 옵션
  - -i : 덮어쓰기 여부를 물음
  - -f : **F**orce, 강제로 덮어씀 (-n과 동일)

## 파일: rm
- <span style="color:red">R</span>e<span style="color:red">m</span>ove file(s)
  - 파일을 삭제
- 사용법
```
 $ rm [옵션] file
```
![그림18](https://ji-hun-park.github.io/assets/images/그림84.jpg "그림18"){: .align-center}

## 작성중








![그림19](https://ji-hun-park.github.io/assets/images/그림85.jpg "그림19"){: .align-center}
![그림20](https://ji-hun-park.github.io/assets/images/그림86.jpg "그림20"){: .align-center}
![그림21](https://ji-hun-park.github.io/assets/images/그림87.jpg "그림21"){: .align-center}
![그림22](https://ji-hun-park.github.io/assets/images/그림88.jpg "그림22"){: .align-center}
![그림23](https://ji-hun-park.github.io/assets/images/그림89.jpg "그림23"){: .align-center}
![그림24](https://ji-hun-park.github.io/assets/images/그림90.jpg "그림24"){: .align-center}
![그림25](https://ji-hun-park.github.io/assets/images/그림91.jpg "그림25"){: .align-center}
![그림26](https://ji-hun-park.github.io/assets/images/그림92.jpg "그림26"){: .align-center}
![그림27](https://ji-hun-park.github.io/assets/images/그림93.jpg "그림27"){: .align-center}
![그림28](https://ji-hun-park.github.io/assets/images/그림94.jpg "그림28"){: .align-center}
![그림29](https://ji-hun-park.github.io/assets/images/그림95.jpg "그림29"){: .align-center}
![그림30](https://ji-hun-park.github.io/assets/images/그림96.jpg "그림30"){: .align-center}
![그림31](https://ji-hun-park.github.io/assets/images/그림97.jpg "그림31"){: .align-center}
![그림32](https://ji-hun-park.github.io/assets/images/그림98.jpg "그림32"){: .align-center}
![그림33](https://ji-hun-park.github.io/assets/images/그림99.jpg "그림33"){: .align-center}

## 마무리
이상으로 이번 실습을 마치겠습니다.
열심히 따라와 주셔서 감사합니다!
