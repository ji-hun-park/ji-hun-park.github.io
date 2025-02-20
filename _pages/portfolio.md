---
title: "Portfolio"
permalink: /portfolio/
layout: single
toc: true
toc_label: "목차"
---

# Game Client Portfolio

## ForTheGranada

### Links
GamePlay Video: [ForTheGranada Youtube](https://www.youtube.com/watch?v=3aYrso13pHI)  
Github: [ForTheGranada Personal](https://github.com/ji-hun-park/ForTheGranada)  
>기존 레파지토리는 private이며, 해당 저장소는 제 개인 저장소입니다.  

상세요구사항명세서: [상세요구사항명세서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%83%81%EC%84%B8%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD%EB%AA%85%EC%84%B8%EC%84%9C_002_GameinhAi.pdf)  
상세설계서: [상세설계서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%83%81%EC%84%B8%EC%84%A4%EA%B3%84%EC%84%9C_002_GameinhAi.pdf)  
최종보고서: [최종보고서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%B5%9C%EC%A2%85%EB%B3%B4%EA%B3%A0%EC%84%9C_GameinhAi.docx)

### 프로젝트 개요
대학교 종합설계(캡스톤) 졸업과제로 제작한 개발자 3인으로 구성된 팀프로젝트 게임입니다.

### 게임 소개
**Unity 2D Top View Adventure Game**  
![그림07](https://ji-hun-park.github.io/assets/images/pofol007.png "그림07"){: .align-center}

몬스터들에게 납치당한 여주인공(Granada)이 흘린 소지품들의 흔적을 찾아 보스를 무찌르고 여주인공을 구출하는 게임입니다.  

**흐름도**  
![그림35](https://ji-hun-park.github.io/assets/images/pofol035.png "그림35"){: .align-center}

### 플레이 방식
![그림08](https://ji-hun-park.github.io/assets/images/pofol008.png "그림08"){: .align-center}
![그림13](https://ji-hun-park.github.io/assets/images/pofol013.png "그림13"){: .align-center}
![그림11](https://ji-hun-park.github.io/assets/images/pofol011.png "그림11"){: .align-center}
![그림12](https://ji-hun-park.github.io/assets/images/pofol012.png "그림12"){: .align-center}

메인 화면에서 게임시작 버튼을 누르면 난이도 선택 창이 나타나며, 쉬움, 보통, 도전 중 하나를 선택하면 스테이지에 진입합니다.  
WASD를 통한 4방향 이동 방식으로 각 스테이지마다 전체 맵의 구조가 랜덤으로 바뀌며, 각 맵마다 보물 상자가 존재합니다.  
공격 기능이 없어 몬스터의 공격을 피해야 합니다.  
몬스터는 부채꼴 모양의 시야각으로 플레이어를 인식하며, 장애물 뒤에 숨으면 인식을 못 합니다.  

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

**흐름도**  
![그림36](https://ji-hun-park.github.io/assets/images/pofol036.png "그림36"){: .align-center}
![그림37](https://ji-hun-park.github.io/assets/images/pofol037.png "그림37"){: .align-center}

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
게임 매니저의 여러 기능들, 게임 클리어와 오버(타임 아웃, HP 0) 기능, LLM API를 활용한 미니게임 퀴즈 구현, UI 구현, 메뉴 기능 구현, 아이템 아이콘 제작, 각 아이템과 관련된 기능들 구현, 각종 사운드 관련 기능 구현, 보스 애니메이션과 AI 구현 등을 맡았습니다.

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

그 밖에도 중복되는 부분을 하나의 함수로 묶거나, Update 문의 호출을 event등을 이용해 줄이는 시도를 했습니다.

## WhatIsAVaccine
[WhatIsAVaccine Play Video](https://youtu.be/HhLgUsnNSzc)
![그림03](https://ji-hun-park.github.io/assets/images/pofol003.png "그림03"){: .align-center}
![그림04](https://ji-hun-park.github.io/assets/images/pofol004.png "그림04"){: .align-center}
![그림05](https://ji-hun-park.github.io/assets/images/pofol005.png "그림05"){: .align-center}
![그림06](https://ji-hun-park.github.io/assets/images/pofol006.png "그림06"){: .align-center}
![그림33](https://ji-hun-park.github.io/assets/images/pofol033.png "그림33"){: .align-center}
![그림34](https://ji-hun-park.github.io/assets/images/pofol034.png "그림34"){: .align-center}

## MonochromeInvader
![그림23](https://ji-hun-park.github.io/assets/images/pofol023.png "그림23"){: .align-center}

## ScatteredTroops
![그림00](https://ji-hun-park.github.io/assets/images/pofol000.png "그림00"){: .align-center}
![그림01](https://ji-hun-park.github.io/assets/images/pofol001.png "그림01"){: .align-center}
![그림02](https://ji-hun-park.github.io/assets/images/pofol002.png "그림02"){: .align-center}

## MakeSomeStory
![그림17](https://ji-hun-park.github.io/assets/images/pofol017.png "그림17"){: .align-center}
![그림18](https://ji-hun-park.github.io/assets/images/pofol018.png "그림18"){: .align-center}
![그림19](https://ji-hun-park.github.io/assets/images/pofol019.png "그림19"){: .align-center}
![그림20](https://ji-hun-park.github.io/assets/images/pofol020.png "그림20"){: .align-center}
![그림21](https://ji-hun-park.github.io/assets/images/pofol021.png "그림21"){: .align-center}

## etc.
![그림22](https://ji-hun-park.github.io/assets/images/pofol022.png "그림22"){: .align-center}
