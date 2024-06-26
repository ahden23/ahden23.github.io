---
layout: post
title: 빌드 자동화 파이프라인 구조 - 2 (feat. 로컬 빌드)
comments: true  
description: 
last_modified_at: 2023-11-13T20:00
date: 2023-11-13 20:00:00 +0900
category: devops
tags: [devops]
---
  
때로는 젠킨스 파이프라인 잡을 생성해서 빌드를 하기보다는, 작업자가 단지 자신의 로컬 워크스페이스 프로젝트에서만 뭔가를 수정하고 로컬에서만 빌드해보길 원하는 경우가 종종 발생한다. 또한 (물론 그래서는 안되겠지만) 빌드 머신에 문제가 생겨서 긴급하게 작업자 pc에서 빌드를 진행해야 하는 경우도 있다.

## 방해요소
그런데 개발을 이어오면서 젠킨스에 의한 빌드 프로세스를 다져가게 되었고, 그러다보니 젠킨스 파이프라인 스크립트 전용으로만 빌드 코드를 작성하게 되었다. 그러다보니, 개발 초기에 유니티 에디터에서 수행했던 커스텀 빌드와는 호환이 안되는 상황에 도달했고 에디터 커스텀 빌드는 이제 쓸모없는 상태다. 정상적인 빌드를 진행하려면 무조건 젠킨스 파이프라인으로만 해야만 했다. 

## 개선
어디서 빌드를 수행하던지 동일하게 빌드를 수행하고 빌드과정의 유지보수도 용이하려면, 공용 빌드모듈을 만드는 리팩토링 작업이 필요했다.  
주요 핵심 내용은 다음과 같다:  
- 우선 빌드 단계에서 일어나는 모든 작업은 (로컬 빌드에서도 실행이 가능하도록) '파이썬 스크립트'로 마이그레이션한다.
- 로컬 빌드를 위한 빌드 실행자(with GUI)를 만든다. 이 빌드 실행자를 통해서 빌드 파라미터 설정하기와 빌드 명령을 실행하기를 할 수 있다.  
<img src="/images/posts/local-build/local-build.png" alt="" style="max-width:70%; height:auto;">

## 결과
이제 기존 젠킨스 파이프라인의 빌드 단계에서 파이썬 빌드 스크립트를 호출하여 수행하게 된다. 또한 로컬 빌드 실행자도 빌드 명령을 통해 같은 파이썬 빌드 스크립트를 실행하여 빌드를 하게 된다.  
앞으로 빌드 과정을 개선하거나 오류수정 이슈가 발생해도 하나의 빌드 모듈(파이썬 스크립트)만 수정하면 된다.
