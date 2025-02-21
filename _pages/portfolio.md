---
title: "Portfolio"
permalink: /portfolio/
layout: single
toc: true
toc_sticky: true
toc_label: "목차"
---

# Game Client Portfolio

## ForTheGranada
### [팀] Unity 2D Top View Adventure Game
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
### Links
Download: [ForTheGranada GoogleDrive](https://drive.google.com/drive/folders/1O5f4WLxdKuyVzdOm2uxCYTrPCpUg5eTf?usp=sharing)  
GamePlay Video: [ForTheGranada Youtube](https://www.youtube.com/watch?v=3aYrso13pHI)  
Github: [ForTheGranada Personal](https://github.com/ji-hun-park/ForTheGranada)  
>기존 레파지토리는 private이며, 해당 저장소는 제 개인 저장소입니다.  

상세요구사항명세서: [상세요구사항명세서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%83%81%EC%84%B8%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD%EB%AA%85%EC%84%B8%EC%84%9C_002_GameinhAi.pdf)  
상세설계서: [상세설계서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%83%81%EC%84%B8%EC%84%A4%EA%B3%84%EC%84%9C_002_GameinhAi.pdf)  
최종보고서: [최종보고서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%B5%9C%EC%A2%85%EB%B3%B4%EA%B3%A0%EC%84%9C_GameinhAi.docx)

### 프로젝트 개요
대학교 종합설계(캡스톤) 졸업과제로 제작한 개발자 3인으로 구성된 팀프로젝트 게임입니다.

### 제작 기간
약 4개월 소요(개인 리팩토링 포함)

### 게임 소개
**Unity 2D Top View Adventure Game**  
![그림07](https://ji-hun-park.github.io/assets/images/pofol007.png "그림07"){: .align-center}

몬스터들에게 납치당한 여주인공(Granada)이 흘린 소지품들의 흔적을 찾아 보스를 무찌르고 여주인공을 구출하는 게임입니다.  

**게임 흐름도**  
![그림35](https://ji-hun-park.github.io/assets/images/pofol035.png "그림35"){: .align-center}

**보스 흐름도**  
![그림36](https://ji-hun-park.github.io/assets/images/pofol036.png "그림36"){: .align-center}
![그림37](https://ji-hun-park.github.io/assets/images/pofol037.png "그림37"){: .align-center}

### 플레이 방식
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">

![그림08](https://ji-hun-park.github.io/assets/images/pofol008.png "그림08"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/pofol013.png "그림13"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/pofol011.png "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/pofol012.png "그림12"){: .align-center}

* 메인 화면에서 게임시작 버튼을 누르면 난이도 선택 창이 나타나며, 쉬움, 보통, 도전 중 하나를 선택하면 스테이지에 진입합니다.  
* WASD를 통한 4방향 이동 방식으로 각 스테이지마다 전체 맵의 구조가 랜덤으로 바뀌며, 각 맵마다 보물 상자가 존재합니다.  
* 공격 기능이 없어 몬스터의 공격을 피해야 합니다.  
* 몬스터는 부채꼴 모양의 시야각으로 플레이어를 인식하며, 장애물 뒤에 숨으면 인식을 못 합니다.  
* 플레이어의 체력이 0이 되거나 제한 시간을 초과하면 게임 오버입니다.

![그림09](https://ji-hun-park.github.io/assets/images/pofol009.png "그림09"){: .align-center}
![그림10](https://ji-hun-park.github.io/assets/images/pofol010.png "그림10"){: .align-center}

보물 상자와 상호작용을 하면 미니게임 퀴즈가 나타나며, 정답을 맞춰 아이템이나 열쇠 조각을 얻을 수 있습니다.  
제한 시간 내에 모든 열쇠조각을 모아 소지품 상자를 열면 스테이지 클리어입니다.  
3개의 일반 스테이지를 모두 클리어하면 보스 스테이지에 진입합니다.

![그림14](https://ji-hun-park.github.io/assets/images/pofol014.png "그림14"){: .align-center}

보스에게 직접 공격은 불가하며, 점프와 대쉬 2가지의 패턴을 랜덤으로 반복합니다.  
보스가 대쉬 패턴 중 장애물(블록)에 부딪치게 될 경우에만 데미지를 입습니다.  
보스의 대쉬는 방향 예고 후 플레이어를 향하므로 방향을 유도해야 합니다.  
점프는 일정시간 사라졌다가 플레이어를 중심으로 다시 나타난 후 원형 범위에 데미지를 가합니다.  

![그림15](https://ji-hun-park.github.io/assets/images/pofol015.png "그림15"){: .align-center}

보스의 HP가 절반 이하가 되면 페이즈2에 돌입해 위치를 미리 알려준 뒤, 맵 상에 불길이 나타납니다.  

![그림40](https://ji-hun-park.github.io/assets/images/pofol040.jpg "그림40"){: .align-center}

보스의 HP가 0이 되면 엔딩 컷씬이 나오면서 게임 클리어입니다.  
</div>
</details>

### 사용 에셋
유료 2D 픽셀 아트 모음 에셋, 음향 에셋, 무료 UI 에셋, 생성형 AI 이미지 등을 사용했습니다.

### 난관들
같이 게임을 만들면서 여러 어려운 점들이 있었습니다.  
서로의 의견이 달라 하나로 모으거나, 연락이 안되거나, 깃 협업 과정에서 충돌이 나는 문제 등이 있었습니다.  

**충돌로 인해 깨진 씬**  
![그림16](https://ji-hun-park.github.io/assets/images/pofol016.png "그림16"){: .align-center}

그래서 주 1회 디스코드 통화를 통해 회의를 진행하고, 수시로 카카오톡을 통해 의견을 나눴습니다.  
각 요소에 대해 각자 의견을 내서 대화를 통해 하나로 수렴했습니다.  
동일한 씬을 여러 사람이 수정하는 것을 최대한 줄이고, 프리팹을 활용하거나, 따로 백업하고 자주 push와 pull을 하는 등의 조치를 취했습니다.

저의 경우 적극적으로 게임 요소에 관한 추가 의견이나, 게임 플레이와 관련된 의견을 냈고, 특히 미니게임의 경우 생성형 AI를 통한 이미지가 100종류 필요한데,  
무료 플랫폼의 경우 계정 당 하루 6종류 제한이라 다른 팀원들의 도움을 적극적으로 요청했습니다.  
최대한 필요할 것 같은 기능들을 전달하고, 이를 적극적으로 만들었고, **가장 많은** 커밋을 했습니다.  
게임 플레이를 직접하면서 경험을 전달하고 수치를 협의 후 조절하는 등으로 적극적으로 임하였습니다.  

**커밋들**  
![그림38](https://ji-hun-park.github.io/assets/images/pofol038.png "그림38"){: .align-center}
![그림39](https://ji-hun-park.github.io/assets/images/pofol039.png "그림39"){: .align-center}

### 맡은 역할
팀원 중 **가장 많은** 역할을 수행했다고 생각합니다.  
게임 매니저의 여러 기능들, 카메라 이동 기능 구현, 게임 클리어와 오버(타임 아웃, HP 0) 기능, LLM API를 활용한 미니게임 퀴즈 구현, UI 구현, 메뉴 기능 구현, 아이템 아이콘 제작, 각 아이템과 관련된 기능들 구현, 각종 사운드 관련 기능 구현, 보스 애니메이션과 AI 구현 등을 맡았습니다.

그 중 가장 핵심이 된 LLM API를 활용한 미니게임 퀴즈 구현에 대해 자세히 설명드리겠습니다.

각 맵 중앙 혹은 구석에 하나만 위치하는 보물 상자에 가까이 다가가 상호 작용 버튼(기본 설정은 F)을 누르면,  
미니게임 전용 UI가 나타나며 화면에는 3개의 이미지와 3개의 보기 선택 버튼이 유니크한 랜덤으로 주어지고, LLM API(여기선 Gemini)가 유추한 1개의 보기가 랜덤한 버튼 위치에 포함됩니다.(API의 응답이 보기와 겹칠 경우 나머지 보기를 다시 뽑습니다)

오답을 고를 시 캐릭터가 뒤로 밀려나며, 5초 간 상자와 상호 작용이 불가해집니다.  
정답을 고르면 열쇠 조각 혹은 아이템 목록 중 랜덤으로 한 개를 얻습니다.

아이템은 맵 상의 모든 상자에 각 스테이지 별 필요한 열쇠 조각 수만큼 먼저 할당한 후,  
나머지 빈 상자에 랜덤으로 아이템을 할당합니다.

**LLM API 관련 코드들**  
![그림24](https://ji-hun-park.github.io/assets/images/pofol024.png "그림24"){: .align-center}

LLM API의 응답은 불규칙하고 오래 걸리기 때문에 응답을 비동기로 받기 위해 코루틴으로 구현했습니다.  
우선 3장의 이미지를 전송해야하는데, 본래 클라우드를 사용해야하나, 비효율적이라 판단해 바이트 스트림으로 보내기로 했습니다.  
해쉬 자료구조를 이용해 유니크한 랜덤 번호 3개를 뽑아 100장의 이미지들 중 해당 번호에 해당하는 이미지의 파일명을 string형 배열에 담았습니다.

![그림25](https://ji-hun-park.github.io/assets/images/pofol025.png "그림25"){: .align-center}

foreach 문을 통해 이미지들의 갯수인 3번만큼 반복하는데,  
각 이미지들은 Texture2D, 읽기 쓰기로 설정했습니다.  
Resoruces 디렉터리에서 파일명에 해당하는 Texture2D를 가져와 변수에 담고,  
바이트 배열에 JPG로 인코딩해 담은 후 이걸 다시 Base64 String으로 변환해 string형 리스트에 추가했습니다.

![그림26](https://ji-hun-park.github.io/assets/images/pofol026.png "그림26"){: .align-center}

필요한 정보인 프롬프트, 인라인 데이터, 최대 토큰 수를 포함해 요청을 보내기 위해 JSON 형식으로 작성했습니다.  
헤더를 작성하고 UnityWebRequest를 이용해 JSON 데이터를 UTF8 인코딩한 바이트 스트림으로 만들어 헤더와 함께 POST 방식으로 apiUrl로 전송했습니다.

![그림27](https://ji-hun-park.github.io/assets/images/pofol027.png "그림27"){: .align-center}

모델의 응답 중 답변 텍스트만 파싱하기 위해 Newtonsoft Json Unity Package를 import해 JObject의 Parse 메서드를 이용했습니다.

![그림31](https://ji-hun-park.github.io/assets/images/pofol031.png "그림31"){: .align-center}

모든 UI와 관련된 기능, 디자인을 구현했습니다.  
에셋을 활용하거나, 생성형 AI로 만들어 그대로 쓰거나 일부 편집해 쓰기도 하고, 그림판3D로 직접 만들기도 했습니다.

### 코드 개선
모든 프로젝트가 종료되고, 무사히 발표를 마친 후,  
저는 개인적으로 추가 공부를 하면서 기존의 저의 코드가 미흡했다는 것을 깨닫고,  
개인 프로젝트를 만들어 리팩토링을 진행했습니다.

![그림28](https://ji-hun-park.github.io/assets/images/pofol028.png "그림28"){: .align-center}
![그림32](https://ji-hun-park.github.io/assets/images/pofol032.png "그림32"){: .align-center}

기존엔 비활성된 UI 오브젝트를 찾을 수 없어 씬에서 전부 활성화시켜놔서 수정할 때마다 번거로웠습니다.  
그래서 자식 오브젝트들을 재귀적으로 찾는 함수를 만들어 비활성화된 자식들도 찾을 수 있게 만들었습니다.  
또한 기존엔 모든 UI 오브젝트들을 GameObject의 FIND로 찾아 비효율적이었으나, canvas의 Transform의 Find로 찾을 수 있게 되었습니다.

![그림29](https://ji-hun-park.github.io/assets/images/pofol029.png "그림29"){: .align-center}

기존엔 모든 스크립트가 그저 Scripts 폴더 하나에 전부 모여있어서 찾기 어려웠는데,  
이를 하위 폴더들을 만들어 분류했습니다.

![그림30](https://ji-hun-park.github.io/assets/images/pofol030.png "그림30"){: .align-center}

기존엔 모든 기능이 게임 매니저 안에 들어가서 게임 매니저가 1000줄이 넘어가 특정 부분 찾기가 어려웠습니다.  
그래서 각 기능 별로 다른 매니저로 나누어 함수들을 분리했습니다.

그 밖에도 중복되는 부분을 하나의 함수로 묶거나, 변수를 읽기 전용으로 제한하거나 상수로 바꾸거나, Update 문의 호출을 event등을 이용해 줄이는 시도를 했습니다.
</div>
</details>

## WhatIsAVaccine
### [개인] Unity 3D 추리 그림 그리기 서바이벌 게임
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
### Links
Download: [WhatIsAVaccine GoogleDrive](https://drive.google.com/file/d/1UxYPVTVv3ubTJkSkJFCheksu-1MviBIz/view?usp=sharing)  
Play Video: [WhatIsAVaccine Play Video](https://youtu.be/HhLgUsnNSzc)  
Github: [WhatIsAVaccine Github](https://github.com/ji-hun-park/Weru_GameJam_R1)  
itch.io: [WhatIsAVaccine itch](https://jihunpark.itch.io/whatisavaccin)

### 프로젝트 개요
사설 게임잼에 참여하기 위해 개인적으로 연습했던 프로젝트들의 기능들을 합쳐봤습니다.  
간단한 커스텀 쉐이더 제작하기, LLM API에게 프롬프트로 질문을 주고 답변 받기, 이미지를 인식시켜 그에 대한 해설을 받기,  
그림판 기능 구현하기, 저장 기능, 캐릭터 이동, 카메라 이동 및 각도 조절 기능 구현, 시간 제한, 몬스터 전투 등을 전부 적용시켰습니다.

### 제작 기간
약 1개월 소요(연습 게임, 코드 개선 포함)

### 게임 소개
**Unity 3D 추리 그림 그리기 서바이벌 게임**  
![그림41](https://ji-hun-park.github.io/assets/images/pofol041.png "그림41"){: .align-center}

어느날 이상한 공간으로 전송된 주인공은 적에 의해 감염된다.  
독이 퍼지기 전에 해당 공간의 AI를 통해 키워드를 알아내 스캐너에게 그림을 전송해 해독제를 얻어야 한다.

### 플레이 방식
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">

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
</div>
</details>

### 사용 에셋
무료 3D 에셋을 사용했습니다.

### 주요 구현 내용
캐릭터 이동을 구현하고 대각선 이동 속도를 정규화했고, 점프와 대쉬 기능과 그를 위한 마나를 구현했습니다.  
카메라 이동 기능을 구현했습니다.  
시간 제한 기능을 구현했습니다.  
몬스터가 플레이어를 인식해 이동하는 것과 투사체 발사 등 전투 기능을 구현했습니다.  
브러쉬 그리기, 색상, 굵기 변경, 다각형 그리기, 영역 선택, 지우기 등 그림판 기능을 구현했습니다.  
그림을 저장하는 기능과 LLM API에게 전송하는 기능을 구현했습니다.  
LLM API에게 질문과 키워드를 전송해 힌트를 답변으로 받는 기능을 구현했습니다.  
힌트들을 볼 수 있는 스크롤 UI를 구현했습니다.  
모델의 응답에서 답변을 파싱해 내용중에서 키워드와 일치하는게 있는지 string 매칭을 해서 일치하는 부분을 빨간색 글씨로 교체합니다.  

![그림33](https://ji-hun-park.github.io/assets/images/pofol033.png "그림33"){: .align-center}

일치하는 부분이 있었으면 클리어 플래그를, 없었으면 페일 플래그를 세웁니다.  
C#과 유니티의 event를 통해 Update문 호출을 줄였습니다.

![그림34](https://ji-hun-park.github.io/assets/images/pofol034.png "그림34"){: .align-center}

어드레스어블을 통해 최적화를 시도했습니다.

### 난관들
캐릭터가 벽을 통과하는 버그가 있어 프로젝트 설정을 변경해 해결했습니다.  
맵이 너무 커서 렌더링이 안되는 부분이 있어 카메라의 far을 조절하고, 벽을 좁혔습니다.

### 코드 개선
빌드 후 적이 너무 빨리 이동하는 현상이 있었는데, 일반 Update 문에서 이동한 것이 문제였기에  
FixedUpdate 문으로 변경해 해결했습니다.
</div>
</details>

## MonochromeInvader
### [팀] Unity 2D 로그 & 소울 라이크 게임
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
### Links
타인의 private로 전환되었기 때문에 없습니다.

### 프로젝트 개요
대학교 게임 동아리 내에서 개발자 3인으로 팀을 꾸려 제작한 게임입니다.  
학기 중에 시작해서 방학까지 이어졌습니다.  
중간에 1명이 탈퇴했고, 스테이지1까지 개발 후 저까지 탈퇴해 개인 프로젝트로 넘겨줬습니다.

### 제작 기간
약 6개월 소요

### 게임 소개
**Unity 2D 로그 & 소울 라이크 게임**  
![그림23](https://ji-hun-park.github.io/assets/images/pofol023.png "그림23"){: .align-center}

흑백과 심플한 디자인을 컨셉으로 외계 침략자가 행성을 침략해 나가는 내용입니다.  
각 행성(에리어)마다 원주민들은 고유한 색을 가지고 있습니다.  
행성(에리어)마다 여러 스테이지가 있습니다.  
엘리트 몬스터를 잡으면 능력치를 올려주는 룬을 얻을 수 있습니다.  
여러 기믹들이 존재하며, 다회차 플레이를 통해 최고점을 갱신할 수 있습니다.

### 플레이 방식
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
WASD, 방향키로 이동하며, 스페이스 바로 점프, 마우스 좌클릭으로 공격이 가능합니다.  
마우스를 길게 눌러 차징 공격이 가능합니다.  
공격에는 코스트가 소모되며, 피격 시에도 코스트가 소모돼, 0이 되면 게임 오버입니다.  
신전이 있으며, 주변에 있으면 점령이 되어 일정 시간동안 코스트가 천천히 회복됩니다.
</div>
</details>
### 난관들
유니티도 아직 익숙지 않았고, 팀플은 처음이었기에 깃 사용이나, 기능 구현이 어려웠습니다.  
그래서 저는 깃을 잘모르는 팀원에게 깃의 기능을 설명해 가르쳐 주었고,  
개인적으로 유튜브에서 영상을 순서대로 시청해 기능들을 익혀나갔습니다.

### 맡은 역할
풀링 시스템, 부활, 룬, 저장 기능 구현
</div>
</details>

## ScatteredTroops
### [개인] Unity 2D TopView 전략 게임
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
### Links
Github: [Scattered-troops Git repository](https://github.com/ji-hun-park/Unity2D-Scattered-troops)  
Download: [Scattered-troops Google Drive](https://drive.google.com/file/d/1mgscL6YTA2cz1Mr8_IMve9qQj_KtViny/view?usp=sharing)

### 프로젝트 개요
스타크래프트에서 산개 기능이 없는 것에 의문을 느껴  
산개 기능을 구현해보고 싶어 제작했습니다.

### 제작 기간
약 일주일 소요

### 게임 소개
**Unity 2D TopView 전략 게임**  
여러 유닛들을 선택해 이동시키거나 산개시킬 수 있습니다.  
해당 기능을 활용해 전투를 유리하게 진행할 수 있습니다.

### 플레이 방식
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
![그림00](https://ji-hun-park.github.io/assets/images/pofol000.png "그림00"){: .align-center}
![그림01](https://ji-hun-park.github.io/assets/images/pofol001.png "그림01"){: .align-center}
![그림02](https://ji-hun-park.github.io/assets/images/pofol002.png "그림02"){: .align-center}

* 마우스 드래그앤 드롭으로 여러 유닛들을 선택할 수 있습니다.
* 마우스를 놓으면 초록색 박스로 영역이 표시된 후 사라지며, 선택된 유닛들은 밑에 주황색 원이 생깁니다.
* 선택된 유닛들은 우클릭으로 이동시킬 수 있습니다.
* 선택된 유닛들은 S를 통해 좁게, D를 통해 넓게 산개시킬 수 있습니다.
* A를 누른 후 적을 누르면 적을 중심으로 원형으로 산개합니다. 다시 A를 눌러 취소할 수 있습니다.
</div>
</details>
</div>
</details>
## MakeSomeStory
### [개인] Unity 2D 이야기 생성 어플리케이션
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
### Links
Github: [MakeSomeStory Git repository](https://github.com/ji-hun-park/ECommerce_Exam)  
Download: [MakeSomeStory Google Drive](https://drive.google.com/drive/folders/1_WlWCnVanbBfses3WpokIHQHxeMNRtRy?usp=sharing)

### 프로젝트 개요
대학교 전자상거래 강의의 과제 제출용으로 제작했습니다.

### 제작 기간
약 일주일 소요(캡스톤 프로젝트 일부를 복사해서 수정 작업 진행)

### 게임 소개
**Unity 2D 이야기 생성 어플리케이션**  
이미지와 컨셉을 입력해 이야기를 생성하는 앱입니다.

### 플레이 방식
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
![그림17](https://ji-hun-park.github.io/assets/images/pofol017.png "그림17"){: .align-center}
![그림18](https://ji-hun-park.github.io/assets/images/pofol018.png "그림18"){: .align-center}
![그림19](https://ji-hun-park.github.io/assets/images/pofol019.png "그림19"){: .align-center}
![그림20](https://ji-hun-park.github.io/assets/images/pofol020.png "그림20"){: .align-center}
![그림21](https://ji-hun-park.github.io/assets/images/pofol021.png "그림21"){: .align-center}

랜덤으로 나온 8장의 이미지 중 원하는 것을 고른 후,  
거기에 맞는 컨셉을 직접 입력 후 전송하면  
LLM이 이야기를 자동으로 생성해 화면에 보여줍니다.  
메뉴에서 생성된 이야기들을 볼 수 있습니다.
</div>
</details>
</div>
</details>
## etc.
<details>
<summary>클릭해서 펼치기 / 접기</summary>
<div markdown="1">
![그림22](https://ji-hun-park.github.io/assets/images/pofol022.png "그림22"){: .align-center}

대학교 동아리 내에서 입문자를 위한 스터디에 참여해 3D 타워디팬스를 제작했습니다.  
해당 스터디에서 끝까지 남아 제작을 완료한 사람은 제가 유일했습니다.  

이후 플래피 버드 클론 코딩, 김치런 챌린지 등에 참가했습니다.

그 밖에 비행 기능, Marching Cubes 알고리즘 구현 등을 연습했습니다.
</div>
</details>
