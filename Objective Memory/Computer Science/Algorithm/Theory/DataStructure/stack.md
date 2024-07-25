---
Date: 2024-07-26
tags:
---
# Prerequisite Algorithm
# Concept

- FILO : First In Last Out, 여러 데이터를 넣으면, 가장 나중에 넣은 데이터가 접근 가능함.
- 선형성 : 데이터를 선형으로 저장함. 양쪽으로 말단을 가짐. 
- 단일 말단 :  접근, 추가, 삭제 모두 단일 말단에만 가능함.
- 값의 추가, 삭제, 접근은 모두 O(1)에 가능함.
# Implementation

## Code

``` C++
#include <stack>

std::stack<int> st;

st.top();    // stack의 최상단 값

st.push();    // stack의 최상단에 값 추가

st.pop();     // stack의 최상단 값 제거

```

스택은 FILO라는 조건만 만족시키면 그 어떤 방법으로도 구현할 수 있다. 일반적으로 3가지 방법으로 구현된다. linked-list, array, deque가 바로 그것이다. 

c++에서 stack은 adaptive-container라 하여 인터페이스만 제공하고, 실재 내부 구현은 여타 컨테이너에게 의존하도록 되어있다. default로 원활한 값의 추가와 삭제를 위해서 deque를 사용한다.

# Analysis

## Time Complexity - O(1)

자료구조의 핵심 동작인 추가, 삭제, 접근 모두 O(1)에 가능함.
## Spatial Complexity - O(N)

단순히 저장하는 자료구조이므로, 원소의 개수에 비례하게 메모리를 점유함.
# Summary

- 재귀적인 특성을 가짐.
- 선형 구조를 띄는 자료구조 중, 단일 말단에만 접근이 가능하다.