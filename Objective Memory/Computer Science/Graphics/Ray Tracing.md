---
Date: 2024-07-29
tags:
---
# Concept

레이트레이싱(Ray Tracing, 이하 RT)은 렌더링 기법의 한 종류로, 빛의 물리적 특성을 모사하여 대상을 표현한다.

## Ray
생명체의 눈은 광원을 출발하여, 물체에 반사된 빛이 눈에 닿는 것으로 동작한다. 빛은 광원으로부터 모든 방향을 향해 출발하며, 빛은 진행 과정에서 만나는 모든 물체에 대해서 **반사, 흡수, 굴절** 등의 이벤트가 발생한다.

하지만, 컴퓨터의 제한된 자원 속에서 빛의 거동을 완벽하게 모사하기란 불가능하다. 렌더링에 중요한 것은 오직 눈에 닿는 빛이며, 이를 구현하기 위해서 눈에서 광원을 향해 돌아가는, 실제 **빛(Ray)의 진행을 역으로 추적(Trace)** 하는  알고리즘이 바로 RT이다.

## Object Collision

## Ray Reflex

## Shadow

어떠한 물체의 표면에 그림자가 지는지 확인하는 방법은 표면에서 광원을 향해서 빛을 쏴보는 것이다. 발사한 빛이 다른 충돌 가능한 물체와 충돌하는지 여부로 판단이 

# Implementation

- [[About MiniRT]]
# Related Subjects

- 3D Object Collision
- Light
- Shadow
- Texture
- Ray Reflex
- Ray Refraction