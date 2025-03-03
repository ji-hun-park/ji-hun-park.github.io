---
title:  "Linux 기초 이론 07 PROCESS2"
excerpt: "Linux 기초 이론 일곱 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-03-03T24:00:00-00:00
---

# Linux Lecture Theory 07 PROCESS2
## Introduction
Process(프로세스)는 대부분 운영 체제 모델에서 기본적인 활성 엔티티입니다.  
이 장에서는 먼저 프로그램 레이아웃, 명령줄 인자,  
프로그램 환경 및 종료 핸들러 문제를 다룹니다.  
그런 다음 이번 장에서는 프로세스 생성,  
프로세스 파괴 및 데몬 프로세스를 포함한 UNIX 프로세스 모델을 다룹니다.

# 5.6 Terminating processes with the *exit* system call
## Process Termination
프로세스가 종료되는 방법은 8가지가 있습니다.  
프로세스 종료는 크게 Normal 과 Abnormal termination으로 나뉩니다.

normal termination은 main 프로그램에서 리턴해서  
최종적으로 _exit()함수를 호출하고 종료합니다.

Abnormal termination은 _exit()에 의해서 종료되지 않는 경우를 말합니다.  
Abort(시스템 콜)를 사용해 종료하는 경우로,  
코(메모리에 있는 모든 변수,스택,힙등) 덤프합니다.

스레드와 관련이 있으며,  
주로 시그널과 관련한 종료로 볼 수 있습니다.

정상적인 종료는 5가지 방법으로 발생합니다.  
- main에서 복귀
- exit 호출
- _exit 또는 _Exit 호출
- 마지막 스레드가 시작 루틴에서 복귀
- 마지막 스레드에서 pthread _exit 호출

비정상적인 종료는 3가지 방법으로 발생합니다.  
- abort 호출
- 신호(signal) 수신
- 마지막 스레드가 취소 요청에 응답

## 작성중
![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG056.jpg "그림01"){: .align-center}
![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG057.jpg "그림02"){: .align-center}
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG058.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG059.jpg "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG060.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG061.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG062.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG063.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG064.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG065.jpg "그림10"){: .align-center}
#### MAXBUF
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG066.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG067.jpg "그림12"){: .align-center}
[MAXBUF](https://ji-hun-park.github.io/linux/Linux-Theory-07/#maxbuf)
![그림13](https://ji-hun-park.github.io/assets/images/LNXIMG068.jpg "그림13"){: .align-center}
![그림14](https://ji-hun-park.github.io/assets/images/LNXIMG069.jpg "그림14"){: .align-center}
![그림15](https://ji-hun-park.github.io/assets/images/LNXIMG070.jpg "그림15"){: .align-center}
![그림16](https://ji-hun-park.github.io/assets/images/LNXIMG071.jpg "그림16"){: .align-center}
![그림17](https://ji-hun-park.github.io/assets/images/LNXIMG072.jpg "그림17"){: .align-center}

## 마무리
이상으로 Linux 이론의 프로세스2편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
