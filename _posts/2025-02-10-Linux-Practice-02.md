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


## 작성중


![그림04](https://ji-hun-park.github.io/assets/images/그림70.jpg "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/그림71.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/그림72.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/그림73.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/그림74.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/그림75.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/그림76.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/그림77.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/그림78.jpg "그림12"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/그림79.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/그림80.jpg "그림14"){: .align-center}
![그림15](https://ji-hun-park.github.io/assets/images/그림81.jpg "그림15"){: .align-center}
![그림16](https://ji-hun-park.github.io/assets/images/그림82.jpg "그림16"){: .align-center}
![그림17](https://ji-hun-park.github.io/assets/images/그림83.jpg "그림17"){: .align-center}
![그림18](https://ji-hun-park.github.io/assets/images/그림84.jpg "그림18"){: .align-center}
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
