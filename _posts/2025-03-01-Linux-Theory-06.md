---
title:  "Linux 기초 이론 06 PROCESS1"
excerpt: "Linux 기초 이론 여섯 번째 페이지"
toc_label: "목차"
categories:
  - Linux
tags:
  - Linux
  - Lecture
  - Theory
last_modified_at: 2025-03-01T24:00:00-00:00
---

# Linux Lecture Theory 06 PROCESS1
## Introduction
Process(프로세스)는 대부분 운영 체제 모델에서 기본적인 활성 엔티티입니다.  
이 장에서는 먼저 프로그램 레이아웃, 명령줄 인자, 프로그램 환경 및 종료 핸들러 문제를 다룹니다.  
그런 다음 이 장에서는 프로세스 생성, 프로세스 파괴 및 데몬 프로세스를 포함한 UNIX 프로세스 모델을 다룹니다.

# 5.0 Program Layout
유닉스 프로그램은 프로그램을 짜고 실행파일을 만들면,  
유닉스 차원에서 부과되는 프로그램의 레이어가 있습니다.  
유닉스는 기본적으로 C언어를 사용합니다.

## main Function
### main Function (1/3)
**Main function’s prototype**  
C언어는 기본적으로 메인(main) 펑션(function)으로 시작한다.  
```c
 int main(int argc, char *argv[]);
```
- **인자들**  
  - argc: 명령어(command-line)를 공백으로 구분해놓은(공백을 통해 나눠놓은) 인자 수
  - argv: 각각의 인자가 들어있는 포인터들의 배열(**argv도 가능)

- exec(굉장히 중요한 시스템 콜): 새로운 프로세스를 만들기 위해 반드시 필요
- 메인이 실행되기 전에 실행되는 특별한 스타트 업 코드가 있다.
- C 프로그램이 exec 함수 중 하나에 의해 커널에 의해 실행될 때, 메인 함수가 호출되기 전에 특별한 시작 루틴(start-up routine)이 호출됨.

```
 user`s shell ->(호출) start up code exit(main(...)); 이후 메인 호출
```
- 실행 가능한 프로그램 파일은 이 루틴을 프로그램의 시작 주소로 지정한다.
- 프로그램을 실행할 때 실행 프로그램 파일이 메인이 아닌 스폐셜 스타트 업 코드에서 실행하게 되어있다(셸이 실행함).
- 이런 처리는 커널에서 알아서 실행해준다.
- 스타트 업 루틴에서 하는 건 **커멘드라인 아규먼트 처리**(argcv)와 **환경 설정**

![그림01](https://ji-hun-park.github.io/assets/images/LNXIMG044.jpg "그림01"){: .align-center}

### main Function (2/3)


### main Function (3/3)

![그림02](https://ji-hun-park.github.io/assets/images/LNXIMG045.jpg "그림02"){: .align-center}
![그림03](https://ji-hun-park.github.io/assets/images/LNXIMG046.jpg "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/LNXIMG047.jpg "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/LNXIMG048.jpg "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/LNXIMG049.jpg "그림06"){: .align-center}
![그림07](https://ji-hun-park.github.io/assets/images/LNXIMG050.jpg "그림07"){: .align-center}
![그림08](https://ji-hun-park.github.io/assets/images/LNXIMG051.jpg "그림08"){: .align-center}
![그림09](https://ji-hun-park.github.io/assets/images/LNXIMG052.jpg "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/LNXIMG053.jpg "그림10"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/LNXIMG054.jpg "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/LNXIMG055.jpg "그림12"){: .align-center}

## 작성중

## 마무리
이상으로 Linux 이론의 프로세스1편을 마치겠습니다.  
긴 글 읽어주셔서 감사합니다!
