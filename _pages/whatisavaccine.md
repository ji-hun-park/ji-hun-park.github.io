---
title: "WhatIsAVaccine"
permalink: /whatisavaccine/
layout: single
toc: true
toc_sticky: true
toc_label: "목차"
---

## Links
Download: [WhatIsAVaccine GoogleDrive](https://drive.google.com/file/d/1UxYPVTVv3ubTJkSkJFCheksu-1MviBIz/view?usp=sharing)  
Play Video: [WhatIsAVaccine Play Video](https://youtu.be/HhLgUsnNSzc)  
Github: [WhatIsAVaccine Github](https://github.com/ji-hun-park/Weru_GameJam_R1)  
~~itch.io: [WhatIsAVaccine itch](https://jihunpark.itch.io/whatisavaccin)~~

## 프로젝트 개요
![그림00](https://ji-hun-park.github.io/assets/images/Game_Loop_with_API.png "그림00"){: .align-center}

사설 게임잼에 참여하기 위해 개인적으로 연습했던 프로젝트들의 기능들을 합쳐봤습니다.  
간단한 커스텀 쉐이더 제작하기, LLM API에게 프롬프트로 질문을 주고 답변 받기, 이미지를 인식시켜 그에 대한 해설을 받기,  
그림판 기능 구현하기, 저장 기능, 캐릭터 이동, 카메라 이동 및 각도 조절 기능 구현, 시간 제한, 몬스터 전투 등을 전부 적용시켰습니다.

## 제작 기간
2025.1~2025.2 약 1개월 소요(연습 게임, 코드 개선 포함)

## 게임 소개
**Unity 3D 추리 그림 그리기 서바이벌 게임**  
![그림41](https://ji-hun-park.github.io/assets/images/pofol041.png "그림41"){: .align-center}

어느날 이상한 공간으로 전송된 주인공은 적에 의해 감염된다.  
독이 퍼지기 전에 해당 공간의 AI를 통해 키워드를 알아내 스캐너에게 그림을 전송해 해독제를 얻어야 한다.

## 플레이 방식

<!--
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
-->

![그림03](https://ji-hun-park.github.io/assets/images/pofol003.png "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/pofol004.png "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/pofol005.png "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/pofol006.png "그림06"){: .align-center}

* WASD로 8방향 이동 가능, Q,E를 통해 이동 방향의 상하, 좌우 반전 가능
* U,I,O,J,K,L 키를 통해 카메라 시점 변경 가능
* 마나를 이용해 점프(스페이스 바)와 대쉬(쉬프트) 가능, 마나는 자동으로 서서히 회복
* 적의 투사체에 맞을 경우 HP감소, HP가 0이 되거나 제한 시간이 지나면 게임 오버
* 맵 구석에 AI가 존재하고, 상호 작용을 통해 각각 1번씩만 질문이 가능(총 5개 존재)
* 답변을 통해 키워드 유추가 가능, 메뉴를 통해 답변을 다시 볼 수 있음
* 맵 중앙 스크린에 상호 작용한 후 캔버스에 키워드를 연상시키는 그림을 그려 저장 후 전송
* 스캐너에게 말을 걸면 그림을 보고 해석한 답변을 내놓고, 키워드와 일치하는 부분은 빨간색으로 표시
* 키워드가 포함되면 클리어, 없으면 게임 오버

<!--
</div>
</details>
-->

## 사용 에셋
무료 3D 에셋을 사용했습니다.

## 주요 구현 내용
- 4방향 캐릭터 이동 구현 및 대각선 이동 속도를 정규화
- 점프와 대쉬 기능과 그를 위한 마나를 구현
- 카메라 이동 및 제어 기능
- 시간 제한 기능
- 몬스터가 플레이어를 인식해 이동, 투사체 발사 등 전투 기능
- 브러쉬 그리기, 색상, 굵기 변경, 다각형 그리기, 영역 선택, 지우기 등 그림판 기능
- 그림을 저장하는 기능과 LLM API에게 전송하는 기능
- LLM API에게 질문과 키워드를 전송해 힌트를 답변으로 받는 기능
- 힌트들을 볼 수 있는 스크롤 UI 구현
- 모델의 응답에서 답변을 파싱해 내용중에서 키워드와 일치하는게 있는지 string 매칭을 해서 일치하는 부분을 빨간색 글씨로 교체하는 기능

![그림33](https://ji-hun-park.github.io/assets/images/pofol033.png "그림33"){: .align-center}

일치하는 부분이 있었으면 클리어 플래그를, 없었으면 페일 플래그를 세웁니다.  
C#과 유니티의 event를 통해 Update문 호출을 줄였습니다.

![그림34](https://ji-hun-park.github.io/assets/images/pofol034.png "그림34"){: .align-center}

어드레스어블을 통해 최적화를 시도했습니다.

## 난관들
캐릭터가 벽을 통과하는 버그가 있어 프로젝트 설정을 변경해 해결했습니다.  
맵이 너무 커서 렌더링이 안되는 부분이 있어 카메라의 far을 조절하고, 벽을 좁혔습니다.

## 코드 개선
빌드 후 적이 너무 빨리 이동하는 현상이 있었는데, 일반 Update 문에서 이동한 것이 문제였기에  
FixedUpdate 문으로 변경해 해결했습니다.

<!--
</div>
</details>
-->
