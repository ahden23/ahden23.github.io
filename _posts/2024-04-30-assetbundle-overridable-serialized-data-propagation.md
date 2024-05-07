---
layout: post
title: "에셋 번들 업데이트 문제: 오버라이드 가능한 직렬화된 데이터 전파 주의하기"
comments: true  
description: >
  에셋번들 의존성과 오버라이드 가능한 데이터 전파
date: 2024-04-30 22:30:05 +0900
category: unity
tags: [unity, assetbundle]
---

결론부터 이야기하면, 에셋번들 내 오버라이드 가능한 직렬화된 데이터가 변경되면 해당 데이터를 참조하는 모든 에셋 번들이 변경된다. 다시 말해 에셋번들의 스크립트나 프리팹 등에서 오버라이드 가능한 직렬화된 데이터가 변경되면, 그 에셋번들에 의존성을 가지는 모든 에셋 번들이 갱신되며 에셋파일 해시값도 변경된다는 말이다. 이것은 업데이트 패치 시에 불필요한 다운로드가 발생하는 문제를 일으킬 수 있으므로 매우 주의해야 한다.

예를 들어, 씬1 안에 프리팹A 오브젝트가 배치되어 있고 씬1과 프리팹A이 각각 자체 에셋번들로 빌드되었다고 하자. 이 때 프리팹A 원본에서 오버라이드 가능한 직렬화 데이터가 변경되면 어떻게 될까? 프리팹A 원본만 변경되었다면 프리팹A만 새로운 에셋번들 빌드가 생성될 것 같지만 사실 그렇지 않다. 의존성 관계가 있는 씬1도 함께 다시 빌드가 된다. 그래서 씬1 에셋 번들 역시 새 빌드가 생성되고 이전과 다른 에셋 파일 해시를 갖게 된다.

실제로 프리팹A의 일부 오버라이드 가능 데이터를 변경하기 전과 후의 씬1 에셋번들 파일을 직접 열어서 비교해보면 씬1 에셋번들의 변경된 부분을 확인할 수 있다. 아래 그림을 보면, 프리팹의 m_RaycastTarget가 0에서 1로 변경되었을 때에 씬도 m_RaycastTarget가 기록되며 프리팹과 마찬가지로 0에서 1로 변경됨을 확인할 수 있다. 
(참고로, 에셋번들 파싱은 엔진에서 제공하는 도구들을 활용하면 에셋번들 데이터를 쉽게 파싱해 볼 수 있다. [여기](https://support.unity.com/hc/en-us/articles/217123266-How-do-I-determine-what-is-in-my-Scene-bundle)를 참고) 

```cmd
***** E:\MYTEST\ASSETBUNDLE\00\PREFABS_DATA\CAB-6fee5e41c4939eed80f81beb3e5e6ebc.txt
        m_Color (1 1 1 1) (ColorRGBA)
        m_RaycastTarget 0 (UInt8)
        m_RaycastPadding (0 0 0 0) (Vector4f)
***** E:\MYTEST\ASSETBUNDLE\01\PREFABS_DATA\CAB-6FEE5E41C4939EED80F81BEB3E5E6EBC.TXT
        m_Color (1 1 1 1) (ColorRGBA)
        m_RaycastTarget 1 (UInt8)
        m_RaycastPadding (0 0 0 0) (Vector4f)
*****
```
```cmd
***** E:\MYTEST\ASSETBUNDLE\0\SCENES_DATA\BuildPlayer-Scene1.txt
        m_Color (1 1 1 1) (ColorRGBA)
        m_RaycastTarget 0 (UInt8)
        m_RaycastPadding (0 0 0 0) (Vector4f)
***** E:\MYTEST\ASSETBUNDLE\1\SCENES_DATA\BUILDPLAYER-SCENE1.TXT
        m_Color (1 1 1 1) (ColorRGBA)
        m_RaycastTarget 1 (UInt8)
        m_RaycastPadding (0 0 0 0) (Vector4f)
*****
```

그런데 만약 씬1 안에서의 프리팹A 오브젝트가 원본 프리팹의 어떤 데이터 값을 이미 오버라이딩한 상태였다면 어떨까? 원본 프리팹의 해당 데이터 값이 어떻게 바뀌건 간에 씬1에서의 오버라이딩된 원래 값이 유지되므로 씬1 에셋번들 내용은 변하지 않을 것으로 추측해 볼 수 있지 않을까? 그렇다. 실제 번들을 빌드해서 테스트해보면, 에셋번들 파일 자체만을 비교했을 때는 완전히 동일하다는 것이 확인된다. 하지만  Unity에셋번들은 의존성에 대한 변경도 고려하여 번들 빌드를 수행하므로, 사실은 새 빌드를 생성하는 것이며 그 해시 역시 다른 값으로 생성된다. 그러니까 새로 빌드 생성은 하지만 결과물은 기존과 같다는 의미이다. 
(Unity에서 생성한 파일 해시값은 변경되지만, 에셋번들 파일에 대하여 직접 md5 해시를 생성하여 비교하면 동일하다.)

