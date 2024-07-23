---
Date: 2024-07-23
tags:
---
# Prerequisite Algorithm
[[Graph]] , [[stack]] , [[queue]] , 
# Concept
- **graph에서 수행하는, 노드에 대한 완전 탐색**
- 그래프 내의 **모든** 노드에 대해서 **중복 없이** 방문하는 방법
- 구현 방법에 따라서 BFS와 DFS의 두 방식이 존재
- 각각 큐와 스택을 사용하여 구현
# Implementation

## Code
### BFS
``` C++
// BFS, adjacent list

std::vector<std::vector<int>> graph(N);

void bfs(int startNode)
{
	std::queue<int> q;
	std::vector<bool> visited(N, false); // bitset to prevent double visit

	visited[startNode] = true;
	q.push(startNode);
	// do something...
	while (!q.empty())
	{
		int curNode = q.front();
		q.pop();
		// do something...
		for (const int &nextNode : graph[curNode]) // check breadth first!
		{
			if (visited[nextNode] == false) // visited check!
			{
				// do something...
				visited[nextNode] = true;
				q.push(nextNode);
			}
		}
	}
}
```
Breadth First Search는 문자 그대로 너비, 즉 인접한 노드를 우선적으로 탐색하는 알고리즘이다. 큐를 활용하여 현재 노드에 인접한 노드를 모두 저장하고, 큐의 FCFS 특성에 따라서 front에 존재하는 노드를 탐색한다. 이러한 FCFS는 시작 노드에서 떨어진 깊이가 낮은 노드를 우선으로 탐색하게 한다.
#### BFS와 최단거리
이러한 BFS의 특성을 활용하면 출발 노드에서 가장 가까이 존재하는 목적지를 찾을 수 있음. 이는 출발 노드를 기준으로 하는 깊이에 대하여, 가까운 노드를 모조리 거쳐야 그 다음 깊이의 노드를 탐색하는 BFS의 특성에 기인함. **단, 그래프에 가중치가 존재하지 않는 경우에 한정됨.**

그래프에 가중치가 존재하는 경우, BFS의 queue를 가중치를 기준으로 하는 priority-queue로 바꿔주면 되는데, 이 알고리즘이 바로 Dijkstra's Algorithm임.

---
### DFS
```C++
// DFS, adjacent list, using call-stack

std::vector<std::vector<int>> graph(N);
std::vector<bool> visited(N, false); // bitset to prevent double visit

void dfs(int curNode)
{
	// do something...
	for (const int &nextNode : graph[curNode])
	{
		if (visited[nextNode] == false)
		{
			// do something...
			visited[nextNode] = true;
			dfs(nextNode);
		}
	}
}

```
Depth First Search는 문자 그대로 깊이를 더해가는 방식을 우선으로 삼는 탐색 알고리즘이다. 스택을 활용하여 진행하며, 스택의 FCLS적 특성에 의해서 스택이 비워지기 전까지는 동일한 깊이의 노드를 탐색하지 않는다. 
### 재귀와 DFS
 DFS를 구현하는 가장 간편한 방법은 재귀recursion을 활용하는 방법이다. 함수의 재귀적 호출에 의해서, 기존의 stack을 call-stack이 대체한다. 하지만, 콜스택은 stack-overflow의 위험이 존재함. 또한 스택 프레임 자체가 증가하므로, 메모리를 과점하는 경향이 있다. 추가적으로 디버깅의 어려움이 따른다. 따라서 dfs를 쓰는 경우에는 문제 상황에 대한 엄밀한 분석이 선행되어야 한다.

평범한 스택을 활용한 dfs구현도 가능하지만, 개인적으로는 bfs를 선호한다.

---
# Analysis

## Time Complexity - O(N)
두 방식 모두 그래프 내에 존재하는 모든 노드를 방문하므로, 시간 복잡도는 노드의 수(N)에 비례한다. 단, 이러한 복잡도를 달성하기 위해서는 필수적으로 **중복 방문을 제거**해줘야 한다.

## Spatial Complexity - O(N)
- BFS는 큐를 사용하며, 최악의 경우는 큐에 모든 노드가 들어가는 경우다. 이는 모든 노드가 다른 모든 노드와 연결되는, 꽉 찬 그래프의 경우 발생할 수 있다. 
- DFS는 (콜)스택을 사용하며, 최악의 경우는 스택에 모든 노드가 들어가는 경우다. 이는 모든 노드가 일렬로 연결되는, 마치 링크드-리스트와 같은 형태에서 발생할 수 있다.
# Summary
상대적인 관계 속에서 정의되는 그래프의 특성 상, 특정 노드의 탐색은 매우 중요하다. 우리는 여러 복잡한 관계를 맺는 대상들을 그래프로 표현하고, 그 관계 속에서 문제를 해결하기를 기대하기 때문이다. 따라서 특정 조건을 만족하는 노드를 찾는 문제는 대단히 일반적이다. 또한 그래프 탐색은 이후에 있을 여러 알고리즘에 대한 기초가 되기에 반드시 외워서 쓸 수 있을 정도가 되어야 한다.
