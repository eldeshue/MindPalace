---
Date: 2024-12-01
tags:
---
# Prerequisite Algorithm
[Graph](Graph), [stack](stack)
# Description
## What is Eulerian Path?
오일러 경로(path)란 그래프 내의 **모든 에지를 중복 없이 지나는 경로**를 뜻한다. 흔히 **한 붓 그리기**라고 부른다. 오일러 경로에서 시작 노드와 종료 노드가 같은 경우, 이를 오일러 회로(Circuit)이라고 한다.
## Existance of Eulerian Path.
주어진 그래프에 대한 오일러 경로의 존재성은 다음과 같은 조건에서 판단된다.

- 1. 단일 연결 요소
	그래프가 단일 연결 요소여야 모든 에지에 접근할 수 있음.
	
- 2. degree
	그래프를 구성하는 노드의 degree가 조건을 만족해야 함.
		
	- 2.1 무향 그래프인 경우
		degree가 홀수인 노드가 없거나(모든 노드에서 시작, 종료 가능, 오일러 회로), 
		단 두 개 존재해야 함(둘이 시작 혹은 끝 노드).

	- 2.2 유향 그래프인 경우
		모든 노드가 in degree와 out degree의 개수가 같거나(오일러 회로)
		out degree가 하나 더 큰 노드(시작)와 in degree가 하나 더 큰 노드(종료) 존제.
		시작, 종료 노드가 여럿 있으면 안됨.
## Hierholzer Algorithm
주어진 그래프에서 오일러 경로를 얻기 위해서 히어홀처 알고리즘이 존재한다.

# Implementation

## Code

``` C++
```

## About Code

# Analysis

## Time Complexity

## Spatial Complexity

# Summary

- 오일러 경로의 존재성 문제 : [[16168. 퍼레이드]]
