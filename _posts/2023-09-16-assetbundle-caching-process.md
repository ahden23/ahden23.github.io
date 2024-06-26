---
layout: post
title: Unity 내장 에셋번들 캐싱 시스템 동작
comments: true  
description: >
  유니티 내장 에셋번들 캐싱 동작 살펴보기
date: 2023-09-16 22:00:00 +0900
category: unity
tags: [unity, assetbundle]
---

유니티 내장 에셋번들 캐싱 시스템 동작을 플로우차트로 정리해보았다:
![Untitled](/images/posts/assetbundle-caching-process/ab-caching-01.png)

## 캐싱 장소
위 그림은 UnityWebRequestAssetBundle.GetAssetBundle 함수를 통해 다운로드 받은 에셋번들을 어떻게 캐싱하는지를 나타내는 그림이다.
최종적으로 디스크 캐싱을 하거나, 해시값이나 버전값을 지정하지 않았을 경우에 에셋번들을 메모리(RAM)에 캐싱한다. 이 때, 만약 LZMA로 압축된 에셋번들을 받았을 경우엔 압축해제하여 메모리에 캐싱한다.

## 디스크 캐싱 형식
디스크 캐싱일 경우, 만약 Caching.CompressEnabled가 true인 경우는 LZ4로 저장된다. 다운로드한 에셋번들이 LZ4가 아닌 경우, LZ4로 바뀌어 압축되는 과정을 거쳐야 한다. Caching.CompressEnabled false라면 비압축 상태로 디스크에 저장된다. 이런 비압축 모드는 너무 용량이 커질 수 있기 때문에 스토리지에 부담을 주기 쉽다.

## 에셋번들 로딩
![Untitled](/images/posts/assetbundle-caching-process/ab-caching-02.png)
이렇게 디스크 또는 메모리에 캐싱된 에셋번들은 에셋번들 로딩함수 호출 시에 에셋번들에 대한 메타데이터를 메모리에 로드한다. (그림에서는 생략했지만) LZ4 에셋번들 캐싱일 경우엔, 청크 단위 압축 정보들이 추가로 로드될 것이다.

## 레퍼런스
[Unity - AssetBundles Cache](https://docs.unity3d.com/2023.3/Documentation/Manual/AssetBundles-Cache.html)  
[Unity - Learn to save memory usage by improving the way you use AssetBundles](https://blog.unity.com/technology/learn-to-save-memory-usage-by-improving-the-way-you-use-assetbundles)  

