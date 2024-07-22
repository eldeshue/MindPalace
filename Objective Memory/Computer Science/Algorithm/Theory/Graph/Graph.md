---
Date: 2024-07-23
tags:
---
# Prerequisite Algorithm
linked-list 
# Concept
- 객체 사이의 **연결**을 표현하는 **자료구조**
- 연결의 주체가 되는 객체를 **node** 혹은 **vertex**, 그 들 사이의 연결을 **edge**
- 연결 관계에 **방향성이 존재하면 유향(Directed)** 그래프, **방향성이 없다면 무향(Undirected)** 그래프
- 중요한 것은 **연결 관계**이며, 오직 **관계 속에서 서로를 정의한다**

---
# Implementation

## Code
링크드 리스트마냥 노드와 포인터를 이용하여 그래프를 구현하는 것도 가능하다. 하지만, 해당 방법은 메모리 파편화와, locality로 인해서 매우 느리다. PS에서는 일반적으로 두 가지 방법(인접 행렬, 인접 리스트)으로 그래프를 구현한다.
``` C++

// Adjacent Matrix
// declaration
std::vector<std::vector<bool>> graph(N, std::vector<bool>(N, false));

// adding edge, between node a and b
graph[a][b] = true;

// check adjacent node
for (int adjNode = 0; adjNode < N; ++adjNode)
{
	if (graph[curNode][adjNode] == true)
	{
		// adjacent node found, do something...
	}
}

```
먼저, 인접 행렬은 위와 같이 구현할 수 있다. 일반적인 환경에서 에지의 개수는 노드의 수의 제곱 이하이므로, 이러한 에지를 모두 예비한 구현이 바로 인접 행렬이다. 

```C++

// Adjacent List
// declaration
std::vector<std::vector<int>> graph(N);

// adding edge, between node a and b
graph[a].push_back(b);

// traverse adjacent node
for (const int &adjNode : graph[curNode])
{
	// do something...
}


```
인접 행렬의 문제는 실제로는 존재하지 않는 에지를 위해서도 메모리를 할당한다는 데에 있다. 이는 메모리 뿐 아니라, 탐색 과정에서 불필요한 조건문을 다수 경험하는 문제가 있다. 이를 해결한 구조가 바로 인접 리스트이다. 인접 리스트는 벡터 등의 자료구조를 활용하여 실제로 연결 관계에 있는, 즉 에지가 존재하는 인접 노드만 저장하는 구조다. 

---
# Analysis

## Time Complexity
그래프 그 자체는 시간 복잡도를 갖지 않음.
## Spatial Complexity - O(N ^ 2)
노드의 개수를 N이라 하면, 각 노드가 서로 완전히 연결되는 경우, 최대 N ^ 2개의 에지를 갖는다.

---
# Summary

그래프 자체로는 대상 사이의 **연결**이라는 매우 단순한 구조이지만, 그래프에서 벌어지는 일의 다양함은 상상조차 하기 어렵다. 세상의 수많은 문제가 그래프로 모델링 하여 접근할 수 있기 때문이다.

세상에 존재하는 몇몇 자료구조는 사실은 그래프의 일부이다. 그래프에서 사이클이 없으면 tree가 되고, tree에서 자식의 수를 하나로 제한하면 링크드-리스트가 된다. 


