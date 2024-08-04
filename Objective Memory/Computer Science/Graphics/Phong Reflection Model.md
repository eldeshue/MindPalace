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

## Object Material
물체의 재질. 퐁 리플렉션 모델을 구성하는, 빛의 3가지 성향에 대해서 어떤 요소가 얼마나 강하게 작용하는지 결정함. 
## Diffuse Reflection
빛의 난반사를 구현한 모델.

현실에서는 같은 피사체라고 하더라도, 빛을 정면으로 받는 곳은 밝고, 비스듬히 받는 곳은 상대적으로 어둡게 느껴진다. Diffuse Reflection은 이처럼 물체가 조명으로부터 받은 빛의 양을 반영하는 모델이다.

빛의 난반사는 물체 표면의 법선 벡터와 조명 벡터 사이에서 결정된다. 물체를 향한 ray의 방향 벡터와 ray가 충돌하는 표면의 법선 벡터, 두 벡터가 이루는 각의 cos값으로 빛의 양을 계산한다. 각의 크기가 크면 빛의 양이 적고, 각의 크기가 작을수록 빛의 양이 크다. 보통 두 노멀벡터 사이의 내적으로 계산한다. 이러한 cos값은 사실 빛의 난반사가 어느 방향으로 얼마나 일어나는지, 그 정도를 의미한다.

``` C++
// 조명벡터, 시선의 도달 위치에서 조명의 위치로 향하는 방향 벡터
vec3 dirToLight = normalize(light.pos - hit.point);
float diff = max(0.0f, dot(dirToLight, hit.normal));
```
만약 법선벡터와 조명벡터의 사잇각의 크기가 90도를 넘는다면, 이는 해당 조명으로부터 빛을 받지 않는 것이 된다. 따라서, 해당 경우에 대해서는 0으로 조정해줘야 한다. 주로 max를 사용한다.

---
## Specular Reflection
빛의 정반사를 구현한 모델로, 반짝거리는 효과를 부여한다.

빛의 정반사는 물체를 항햐는 시선의 벡터와 표면에서 반사된 조명의 벡터 사이에서 결정된다. 정반사 정도를 결정하는 값은 조명의 반사벡터와 시선 벡터 사이의 cos값을 취한다.

``` C++
// 반사 방향 벡터, 조명 벡터를 법선벡터에 대칭시킨다.
vec3 reflectDir = dirToLight - 2.0f * (dirToLight - dot(hit.normal, dirToLight) * hit.normal);
```
빛의 정반사에는 빛이 얼마나 잘 퍼지는 지에 대한 부분도 고려해야 한다. 이는 재질의 성질이며 specular power라고 하며, 흔히 alpha라 쓴다. 해당 값이 높으면 높을수록, 정반사가 덜 되어서 빛이 모이는 효과가 나고, 작을수록 빛이 퍼지는 효과가 난다.
``` C++
// specular light coefficient
float specular = pow(std::max(0.0f, dot(-ray.dir, normalize(reflectDir))), material->alpha);
```
---
## Ambient Light
조명이 없어도, 물체 그 자체가 발하는 색. 현실의 모든 물체는 빛을 반사하는 것으로 그 색이 드러나지만, 퐁 리플렉션 모델에서 amb는 물체 그 자체가 내는 빛의 색을 의미한다. 따라서 피사체의 색으로 설정된 값을 그대로 가져온다. 현실과는 다른 부분이다.

이상의 효과를 모두 적용한 부분은 다음과 같다.

``` C++
	material->amb + material->diff * diff + material->spec * specular;
```
보통 ambient light값은 material의 고정된 값으로 주어진다. 

---
