---
title: "FortheGranada"
permalink: /forthegranada/
layout: single
toc: true
toc_sticky: true
toc_label: "목차"
---

**"Generative AI API를 활용하여 매번 새로운 퀴즈를 생성하는 시스템을 구축했습니다."**

![움그00](https://ji-hun-park.github.io/assets/images/ftgplay.gif "움그00"){: .align-center}

## 0. Links
📥**Download:** [ForTheGranada GoogleDrive](https://drive.google.com/drive/folders/1O5f4WLxdKuyVzdOm2uxCYTrPCpUg5eTf?usp=sharing)  
🎬**Game Introduction Video:** [ForTheGranada Youtube](https://www.youtube.com/watch?v=3aYrso13pHI)  
💻**Github:** [ForTheGranada Personal](https://github.com/ji-hun-park/ForTheGranada)  
>원본 팀 프로젝트는 Private이며, 리팩토링 코드는 본 링크에서 확인 가능합니다.  

### Documents
📋**상세요구사항명세서:** [상세요구사항명세서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%83%81%EC%84%B8%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD%EB%AA%85%EC%84%B8%EC%84%9C_002_GameinhAi.pdf)  
📋**상세설계서:** [상세설계서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%83%81%EC%84%B8%EC%84%A4%EA%B3%84%EC%84%9C_002_GameinhAi.pdf)  
📋**최종보고서:** [최종보고서](https://github.com/ji-hun-park/ForTheGranada/blob/main/%EC%B5%9C%EC%A2%85%EB%B3%B4%EA%B3%A0%EC%84%9C_GameinhAi.docx)

## 1. 프로젝트 개요
**생성형 AI(Gemini API)를 활용한 실시간 퀴즈 생성 시스템이 도입된 2D 탑뷰 어드벤처 게임**으로  
대학교 종합설계(캡스톤)에서 졸업과제로 제작한 개발자 3인으로 구성된 팀프로젝트입니다.

### 📋프로젝트 정보

* **장르 / 플랫폼:** 2D Top-View Adventure / PC (Single)
* **개발 인원:** 3인 개발(개발자 3인(팀장 1, 팀원 2), 클라이언트 및 시스템 담당, 개인 기여도 40% 이상)
* **제작 기간:** 2024.8 ~ 2024.12 사전 기획 포함 약 4개월 소요
    * 2025.1 ~ 2025.2 개인 리팩토링 1개월
* **사용 엔진 / 언어 / 환경:** Unity6 (6000.0.32f1) / C# / VSCode(본인) & Visual Studio 2022(팀원들)

### 핵심 역할
* Google Gemini API 연동 및 비동기 통신 구현
* FSM 기반 보스 AI 및 패턴 설계
* 전체 게임 매니지먼트 및 데이터 구조 설계

## 2. 게임 소개
**Unity 2D Top View Adventure Game**  

**Core Loop:** 탐험(몬스터 회피) -> 열쇠 조각 수집(퀴즈) -> 보스전 -> 엔딩

![그림07](https://ji-hun-park.github.io/assets/images/pofol007.png "그림07"){: .align-center}

몬스터들에게 납치당한 여주인공(Granada)이 흘린 소지품들의 흔적을 찾아 보스를 무찌르고 여주인공을 구출하는 게임입니다.  

**게임 흐름도**  
![흐름도_그라나다](https://ji-hun-park.github.io/assets/images/Flowchart_Forthegranada.png "flowchart_granada"){: .align-center}

### 플레이 방식

**메인 스테이지**
![그림08](https://ji-hun-park.github.io/assets/images/pofol008.png "그림08"){: .align-center}

**랜덤 맵 생성**
![그림13](https://ji-hun-park.github.io/assets/images/pofol013.png "그림13"){: .align-center}

**일시 정지 메뉴**
![그림11](https://ji-hun-park.github.io/assets/images/pofol011.png "그림11"){: .align-center}

**음량 조절**
![그림12](https://ji-hun-park.github.io/assets/images/pofol012.png "그림12"){: .align-center}

* 메인 화면에서 게임시작 버튼을 누르면 난이도 선택 창이 나타나며, 쉬움, 보통, 도전 중 하나를 선택하면 스테이지에 진입합니다.  
* WASD를 통한 4방향 이동 방식으로 각 스테이지마다 전체 맵의 구조가 랜덤으로 바뀌며, 각 맵마다 보물 상자가 존재합니다.  
* 공격 기능이 없어 몬스터의 공격을 회피해야 합니다.  
* 몬스터는 부채꼴 모양의 시야각으로 플레이어를 인식하며, 장애물 뒤에 숨으면 인식을 못 합니다.  
* 플레이어의 체력이 0이 되거나 제한 시간을 초과하면 게임 오버입니다.

**미니 게임**
![그림09](https://ji-hun-park.github.io/assets/images/pofol009.png "그림09"){: .align-center}

**아이템 획득**
![그림10](https://ji-hun-park.github.io/assets/images/pofol010.png "그림10"){: .align-center}

보물 상자와 상호작용을 하면 미니게임 퀴즈가 나타나며, 정답을 맞춰 아이템이나 열쇠 조각을 얻을 수 있습니다.  
제한 시간 내에 모든 열쇠조각을 모아 소지품 상자를 열면 스테이지 클리어입니다.  
3개의 일반 스테이지를 모두 클리어하면 보스 스테이지에 진입합니다.

**보스 스테이지**
![그림14](https://ji-hun-park.github.io/assets/images/pofol014.png "그림14"){: .align-center}

보스에게 직접 공격은 불가하며, 점프와 대쉬 2가지의 패턴을 랜덤으로 반복합니다.  
보스가 대쉬 패턴 중 장애물(블록)에 부딪치게 될 경우에만 데미지를 입습니다.  
보스의 대쉬는 방향 예고 후 플레이어를 향하므로 방향을 유도해야 합니다.  
점프는 일정시간 사라졌다가 플레이어를 중심으로 다시 나타난 후 원형 범위에 데미지를 가합니다.  

**2페이즈 불길 생성**
![그림15](https://ji-hun-park.github.io/assets/images/pofol015.png "그림15"){: .align-center}

보스의 HP가 절반 이하가 되면 페이즈2에 돌입해 위치를 미리 알려준 뒤, 맵 상에 불길이 나타납니다.  

**엔딩 씬**
![그림40](https://ji-hun-park.github.io/assets/images/pofol040.jpg "그림40"){: .align-center}

보스의 HP가 0이 되면 엔딩 컷씬이 나오면서 게임 클리어입니다.  

### Assets Used
2D 픽셀 아트 캐릭터 및 장식물 에셋, 음향 에셋, UI 에셋, 생성형 AI 이미지 등을 사용했습니다.  
아이템의 경우 관리의 유용성을 위해 ScriptableObject로 구현했습니다.

게임의 일관성과 비용 절감을 위해 유료 이미지가 아닌 생성형 AI 이미지를 활용하였고,  
통신 과정에서 발생하는 지연 시간과 이미지 파일들의 사이즈를 고려하여  
클라우드가 아닌 파일 시스템을 사용하였습니다.

### 협업 난관
깃 협업 과정에서 Git 충돌이 나는 문제, 한글이 깨지는 문제,  
프리팹 미적용, 코드 기능 상충, 미지의 기술 등의 문제가 있었습니다.  

**충돌로 인해 깨진 씬**  
![그림16](https://ji-hun-park.github.io/assets/images/pofol016.png "그림16"){: .align-center}

## 3. 해결 과정
소통 문제 해결을 위해 주 1회 이상 디스코드 통화를 통해 회의를 진행하고, 수시로 카카오톡을 통해 의견을 나눴습니다.  
회의마다 각 요소에 대한 의견을 내서 대화를 통해 하나로 수렴했습니다.  
인코딩 충돌로 한글이 깨지는 문제 해결을 위해 인코딩을 통일했습니다.  
코드 일관성을 위해 코드 컨벤션을 도입했습니다.  
동일한 씬을 여러 사람이 수정하는 것을 최대한 줄이기 위해 작업 영역을 분리하고, 프리팹을 활용하거나,  
각자 따로 백업하고 자주 Git push와 pull을 하고 PR을 날리는 등의 조치를 취했습니다.

## 4. 개인 활약
### 핵심
일부 몰랐던 기능이 존재했고, 이를 LLM과 구글링을 통해 해결하였습니다.  
특히 Unity에서 LLM API를 활용하는 기술은 최신 기술이기에 낯설었지만,  
ChatGPT를 통해 기본 틀을 알아낸 후 이상한 부분, 대표적으로 API키 부분과  
URL, JSON이 이상했는데 이를 검색을 통해 수정했습니다.  
또한 패키지가 필요한 점을 깨닫고 검색을 통해 패키지를 추가하기도 했습니다.  
이후 여러 시행 착오를 통해 기능을 구현하는데 성공했습니다.

### 기타 협업 경험
적극적으로 게임 요소에 관한 추가 의견이나, 게임 플레이와 관련된 의견을 냈고,  
특히 미니게임의 경우 생성형 AI를 통한 이미지가 100종류 필요한데,  
무료 플랫폼의 경우 계정 당 하루 6종류 제한이라 다른 팀원들의 도움을 적극적으로 요청했습니다.  
최대한 필요할 것 같은 기능들을 전달하고, 이를 적극적으로 만들었고, 가장 많은 커밋을 했습니다.  
게임 플레이를 직접하면서 경험을 전달하고 수치를 협의 후 조절하는 등으로 적극적으로 임하였습니다.  

### 주요 담당 역할 (Contribution)
팀원 중 가장 많은 종류의 역할(40% 이상의 기여도)을 수행했습니다.  
- Core Systems: 게임 매니저, 게임 클리어 오버 시스템, 카메라 이동 제어, 씬 전환, 데이터 관리 시스템 구축
- GenAI Integration: Google Gemini API를 활용한 실시간 퀴즈 생성 시스템 및 비동기 통신 구현
- Gameplay: 인터랙션 시스템, 보스 애니메이션과 FSM 및 패턴 AI, UI/UX 로직, 음향 관리, 아이템 제작
- Optimization: 재귀적 탐색을 통한 UI 관리 최적화, 상자 아이템 배치 최적화, 모듈화를 통한 결합도 감소 (리팩토링)

그 중 가장 **핵심**이 된 LLM API를 활용한 미니게임 퀴즈 구현에 대해 자세히 설명드리겠습니다.

**Gemini API**  
- JSON 파싱(Newtonsoft.Json) 및 비동기 통신(UnityWebRequest) 구현
- Texture2D -> JPG(PNG라면 압축해제) -> Base64 변환 최적화로 이미지 전송 지연 최소화

![그림39](https://ji-hun-park.github.io/assets/images/AIAPICHART.png "그림39"){: .align-center}

각 맵 중앙 혹은 구석에 하나만 위치하는 보물 상자에 가까이 다가가 상호 작용 버튼(기본 설정은 F)을 누르면,  
미니게임 전용 UI가 나타나며 화면에는 3개의 이미지와 3개의 보기 선택 버튼이 유니크한 랜덤으로 주어지고, LLM API(Gemini)가 유추한 1개의 보기가 랜덤한 버튼 위치에 포함됩니다.(API의 응답이 보기와 겹칠 경우 나머지 보기를 다시 뽑습니다)

오답을 고를 시 캐릭터가 뒤로 밀려나며, 5초 간 상자와 상호 작용이 불가해집니다.  
정답을 고르면 열쇠 조각 혹은 아이템 목록 중 랜덤으로 한 개를 얻습니다.

아이템은 맵 상의 모든 상자에 각 스테이지 별 필요한 열쇠 조각 수만큼 먼저 할당한 후,  
나머지 빈 상자에 랜덤으로 아이템을 할당합니다.  
해당 과정에서 씬 이동 시 유니티 프로그램이 정지하는 문제가 발생했습니다.  
이를 해결하기 해당 씬 진입 시 각각의 상자 오브젝트를 찾는 코드를 최적화하고 문제가 되는 범위를 넘어서는 로직을 수정했습니다.

**LLM API 관련 코드와 과정**  
![그림24](https://ji-hun-park.github.io/assets/images/pofol024.png "그림24"){: .align-center}

**비동기 통신 구현**  
LLM API의 응답은 불규칙하고 오래 걸리기 때문에 응답을 비동기로 받기 위해 코루틴으로 구현했습니다.  

**이미지 처리**  
우선 3장의 이미지를 전송해야하는데, 본래 클라우드를 사용해야하나, 비효율적이라 판단해 바이트 스트림으로 보내기로 했습니다.  
해쉬 자료구조를 이용해 유니크한 랜덤 번호 3개를 뽑아 100장의 이미지들 중 해당 번호에 해당하는 이미지의 파일명을 string형 배열에 담았습니다.

![그림25](https://ji-hun-park.github.io/assets/images/pofol025.png "그림25"){: .align-center}

foreach 문을 통해 이미지들의 갯수인 3번만큼 반복하는데,  
각 이미지들은 Texture2D, 읽기 쓰기로 설정했습니다.  
Resoruces 디렉터리에서 파일명에 해당하는 Texture2D를 가져와 변수에 담고,  
바이트 배열에 JPG로 인코딩해 담은 후 이걸 다시 Base64 String으로 변환해 string형 리스트에 추가했습니다.

![그림26](https://ji-hun-park.github.io/assets/images/pofol026.png "그림26"){: .align-center}

**데이터 파싱**  
필요한 정보인 프롬프트, 인라인 데이터, 최대 토큰 수를 포함해 요청을 보내기 위해 JSON 형식으로 작성했습니다.  
헤더를 작성하고 UnityWebRequest를 이용해 JSON 데이터를 UTF8 인코딩한 바이트 스트림으로 만들어 헤더와 함께 POST 방식으로 apiUrl로 전송했습니다.

![그림27](https://ji-hun-park.github.io/assets/images/pofol027.png "그림27"){: .align-center}

모델의 응답 중 답변 텍스트만 파싱하기 위해 Newtonsoft Json Unity Package를 import해 JObject의 Parse 메서드를 이용했습니다.

**예외 처리**  
데이터 통신 실패 시에 대한 대응으로 미리 설정된 이미지 셋과 답변이 세팅되도록 설정했습니다.

**UI**  
![그림31](https://ji-hun-park.github.io/assets/images/pofol031.png "그림31"){: .align-center}

모든 UI와 관련된 기능, 디자인을 구현했습니다.  
에셋을 활용하거나, 생성형 AI로 생성해 원본 혹은 일부 편집하여 사용했으며, 그림판3D로 직접 제작한 부분도 있습니다.

## 5. 코드 개선(Refactoring & Optimization)
모든 프로젝트가 종료되고, 무사히 발표를 마친 후,  
저는 개인적으로 추가 공부를 하면서 기존의 저의 코드가 미흡했다는 것을 깨닫고,  
개인 프로젝트를 만들어 리팩토링을 진행했습니다.

![그림28](https://ji-hun-park.github.io/assets/images/pofol028.png "그림28"){: .align-center}

Before: 비활성된 UI 오브젝트를 찾을 수 없어 씬에서 전부 활성화시켜놔서 수정할 때마다 번거로웠습니다.  
After: 자식 오브젝트들을 재귀적으로 찾는 함수를 만들어 비활성화된 자식들도 찾을 수 있게 만들었습니다.

![그림32](https://ji-hun-park.github.io/assets/images/pofol032.png "그림32"){: .align-center}

Before: 모든 UI 오브젝트들을 GameObject의 FIND로 찾아 비효율적이었습니다.  
After: canvas의 Transform의 Find로 찾을 수 있게 되었습니다.

![그림29](https://ji-hun-park.github.io/assets/images/pofol029.png "그림29"){: .align-center}

Before: 모든 스크립트가 Scripts 폴더 하나에 전부 모여있어서 찾기 어려웠습니다.  
After: 하위 폴더를 만들어 분류했습니다.

![그림30](https://ji-hun-park.github.io/assets/images/pofol030.png "그림30"){: .align-center}

Before: 모든 기능이 게임 매니저 안에 들어가서 게임 매니저가 1000줄이 넘어가 특정 부분 찾기가 어려웠습니다.  
After: 각 기능 별로 별도의 매니저로 나누어 함수들을 분리했습니다.

그 밖에도 중복되는 부분을 하나의 함수로 묶거나, 변수를 읽기 전용으로 제한하거나 상수로 바꾸거나, Update 문의 호출을 event등을 이용해 줄이는 시도를 했습니다.
