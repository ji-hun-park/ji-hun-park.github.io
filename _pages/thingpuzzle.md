---
title: "ThingPuzzle"
permalink: /thingpuzzle/
layout: single
toc: true
toc_sticky: true
toc_label: "목차"
---

## 0. Links
📥**Download:** [ThingPuzzle Google Drive](https://drive.google.com/file/d/1BjPiNzjDdqVDAp1kldkE8xpYaWbFIee0/view?usp=sharing)  
💻**Github:** [ThingPuzzle Git repository](https://github.com/ji-hun-park/puppy_puzzle)  

## 1. 프로젝트 개요
입사 과제로 제작한 모바일 퍼즐 게임입니다.

### 📋프로젝트 정보

* **장르 / 플랫폼:** 2D 퍼즐 게임 / Mobile (Single)
* **개발 인원:** 1인 개발, 클라이언트 및 시스템 담당
* **제작 기간:** 2025.6 ~ 2025.6 1일 소요(과제 기한)
* **사용 엔진 / 언어 / 환경:** Unity6 (6000.0.32f1) / C# / VSCode

## 2. 게임 소개
![그림00](https://ji-hun-park.github.io/assets/images/thingpuzzle.png "그림00"){: .align-center}

**Unity 2D Mobile Puzzle Game**  
8X8의 보드에서 4칸짜리 다양한 모양의 블록을 옮겨 한 라인을 채워 없애는 퍼즐 게임입니다.

## 3. 플레이 방식

3개의 영역에 4칸 분량의 다양한 모양을 가진 블럭이 랜덤으로 주어집니다.  
블럭을 드래그 앤 드롭으로 8X8 보드 안에 옮겨 채웁니다.  
하나의 라인을 가로 세로 상관없이 다 채우면  
해당 라인의 블럭이 전부 사라지며, 점수를 획득합니다.  
더 이상 블럭을 넣을 수 없으면 게임오버입니다.

## 4. 구현 방식
보드는 2차원 배열을 이용해 구현했습니다.  
블록 드롭 시 가장 가까운 보드의 칸을 중심으로  
상하좌우에 칸이 비었는지, 가장자리인지 여부를 체크합니다.  
들어갈 수 없을 시 원래 Position으로 돌아갑니다.  
블록이 배치되면 각 라인별로 가득 찼는지 체크합니다.  
한 라인이 가득찼으면 전부 빈 블럭으로 초기화하고  
UI와 연동된 점수를 증가시킵니다.
