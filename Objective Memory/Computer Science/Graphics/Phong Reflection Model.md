---
Date: 2024-08-02
tags:
---
# Prerequisite Algorithm
# Concept
- Ambient, Diffuse, Specular의 3가지 빛을 결합한 빛 반사 모델.
- 실제 빛의 반사와는 다른, 결과 만을 모사한 효율적인 표현법.
- Phong은 알고리즘을 고안한 사람의 이름임.
- 퐁 쉐이딩은 같은 사람이 만든 픽셀 단위 법선벡터 보간 알고리즘.
## Ambient Light
조명이 없어도, 물체 그 자체가 발하는 색. 현실의 모든 물체는 빛을 반사하는 것으로 그 색이 드러나지만, 퐁 리플렉션 모델에서 amb는 물체 그 자체가 내는 빛의 색을 의미한다. 따라서 피사체의 색으로 설정된 값을 그대로 가져온다. 현실과는 다른 부분.

단순히 물체가 내는 색을 가져오는 것에 불과하므로, 그 조명에 의한 그 어떠한 영향(반사광, 원근감, 입체감 등)을 제공하지 않는다.
## Diffuse Reflection


## Specular Reflection
# Implementation

## Code

``` C++
```

## About Code

# Analysis

## Time Complexity

## Spatial Complexity

# Summary
