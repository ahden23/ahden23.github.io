---
layout: post
title: "아트팀 맞춤형: 최적화 가이드 - 메시 최적화 4. FBX 모델 임포트 옵션"
comments: true  
description: >
  임포트 옵션도 신경써야 한다.
date: 2022-12-07 13:00:00 +0900
category: optimization
tags: [optimization]
---

모델 임포트 옵션을 적절히 설정하면, 리소스 메모리 절약을 비롯한 여러 성능 개선에 영향을 줄 수 있습니다. 몇 가지를 살펴보도록 하죠.

![Untitled](/images/posts/art-team-fbx-import-setting/g01.png)

## Read/Write Enabled은 꺼주세요
기본적으로 Read/Write Enabled 옵션은 비활성화해야 합니다. 그렇지 않으면, 이 모델의 데이터 메모리는 게임 플레이 중에 2배를 차지하게 됩니다. (단, 유니티 스크립트를 통해 해당 모델 메시에 접근하는 구현이 포함된 경우는 예외적으로 옵션을 활성화해야 합니다.)   

## Optimize Mesh는 Everything으로 해주세요
이 옵션을 켜면, 엔진에서 더 좋은 GPU 성능을 내기 위해 삼각형 인덱스와 순서를 적절히 변경합니다. 특별한 이유가 없는 한 옵션값을 Everything으로 설정합니다.

## Normal값은 임포트하지 마세요
우리 프로젝트는 조명을 사용하지 않는 2D기반 게임이기 때문에, 기본적으로 모델 리소스로부터 노멀값 및 탄젠트값을 임포트할 필요가 없습니다. 만약 노멀값과 탄젠트값을 임포트하게 되면, 게임 플레이 중에 그만큼 불필요한 메모리 대역폭만 낭비됩니다.