---
Date: 2024-07-27
tags:
---
# Prerequisite Algorithm
# Concept
- FIFO : First In First Out, 자료구조에 삽입한 순서 대로 제거할 수 있음.
- Linearity : 데이터를 선형으로 저장함. 순차 순회. 양쪽에 말단을 가짐.
- Double End : 자료구조의 양 단말로 접근이 가능함.
- Constancy : 데이터의 추가, 삭제, 접근 모두 상수 시간에 가능함.
# Implementation

## Code

``` C++
#include <queue>

std::queue<T> q;

q.front(); // 다음으로 pop 될 원소에 접근

q.back();  // 가장 마지막으로 push된 원소에 접근

q.push(data);  // 큐의 뒤에 원소 추가

q.pop();       // 큐의 앞에서 원소 제거

```
큐는 FIFO라는 개념을 만족하면 어떤 방법으로 구현해도 좋다. 일반적으로 deque로 구현된다.

c++에서 queue는 adaptive-container로, 실제 내부 구현은 다른 컨테이너에 의존한다. 마찬가지로 default는 deque로 선택된다.
## About Code

# Analysis

## Time Complexity

자료구조의 핵심 동작인 추가, 삭제, 접근 모두 O(1)에 가능함.

자료구조 내부의 순회를 위해서는 O(N)이 필요하다.
## Spatial Complexity - O(N)

단순히 저장하는 자료구조이므로, 원소의 개수에 비례하게 메모리를 점유함.
# Summary
