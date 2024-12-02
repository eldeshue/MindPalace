---
Date: 2024-12-01
tags:
---
# Prerequisite Algorithm
 [stack](stack), [Graph](Graph), [[GraphSearch | DFS]]
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
# Implementation of Hierholzer Algorithm

## Code

``` C++

// adjMatrix, recursive
std::vector<int> eulerPath;
std::vector<std::vector<int>> adjMatrix(N, std::vector<int>(N, 0));

std::function<void(int)> dfsMat = [&](const int curNode)
{
	for (int nextNode = 0; nextNode < N; ++nextNode)
	{
		while (adjMatrix[curNode][nextNode])
		{
			adjMatrix[curNode][nextNode]--;
			//adjMatrix[nextNode][curNode]--; // undirected edge, needed
			dfsMat(nextNode);
		}
	}
	eulerPath.push_back(curNode);
};
std::reverse(eulerPath.begin(), eulerPath.end()); // directed graph, reverse

// adjList, recursive
std::vector<int> eulerPath;
std::vector<std::deque<int>> adjList(N);

std::function<void(int)> dfsList = [&](const int curNOde)
{
	while (!adjList[curNode].empty()) // if the adj list is vector, use iteratioin
	{
		const int nextNode = adjList[curNode].front();
		adjList[curNode].pop_front(); // directed edge
		dfsList(nextNode);
	}
	eulerianPath.push_back(curNode);
};
std::reverse(eulerPath.begin(), eulerPath.end()); // directed graph, reverse

// adjList, non-recursive dfs
std::function<void(int)> nonRecurseDfsList = [&](const int startNode)
{
	std::stack<int> nodeStack;

	// Iterative DFS using stack
	nodeStack.push(startNode);
	while (!nodeStack.empty())
	{
		const int curNode = curNodeStack.top();
		if (!adjList[curNode].empty())
		{
			// Visit the next node
			int nextNode = adjList[curNode].front();
			adjList[curNode].pop_front(); // directed edge
			nodeStack.push(nextNode);
		}
		else
		{
			// No more neighbors to visit, add node to result
			result.push_back(curNode);
			nodeStack.pop();
		}
	}
	std::reverse(eulerPath.begin(), eulerPath.end()); // directed graph, reverse
};

```

## About Code
핵심 아이디어는 그래프의 **edge와 node의 역전**이다.

먼저, 탐색 경로가 오일러 경로가 되기 위해서는 해당 그래프에 존재하는 모든 edge를 방문해야 한다. 이 때, **모든 edge를 방문하기 위해서** 노드에 대한 방문 여부는 체크하지 않는다. **오직 edge에 대한 방문 여부를 체크한다**. 이후 순회한 edge를 구성하는 노드에 대하여 방문 순서대로 수집해주면 된다.

다만, 그래프 탐색(DFS)의 기준은 노드이므로, 순서대로 edge를 수집하기 위해서는 인접한 모든 edge가 방문 완료된 노드를 수집한다. 

만약 dfs과정에서 방문 완료되지 않은 edge가 존재하는 노드를 수집했는데,  해당 노드에 다시 도달할 경로가 없는 경우, 해당 edge는 영원히 수집할 수 없기 때문이다.

다만, DFS의 재귀적 특성에 의해서 경로를 구성하는 노드는 방문한 edge의 역순으로 수집하므로, 마지막에 reverse해줘야 한다. 단, 무향 그래프에서는 모든 에지가 양방향이므로, reverse를 하지 않아도 된다.

보통 adjMatrix로 그래프를 구성한 후, recursive한 DFS를 통해서 구현하는 편이다. 이 방식이 인접 행렬의 특성 상 조금 느릴 수 있지만, 에지의 개수에 따른 메모리 소모에도 자유롭고, 또 구현이 자연스럽다.
# Analysis

## Time Complexity - O(V + E)

재귀, 비재귀 여부는 dfs의 구현 방식의 문제이므로, 시간 복잡도에는 영향을 주지 않음.
## Spatial Complexity - O(V + E) or O(V^2)
adj list를 사용한 경우, V개의 deque와 E의 2배 만큼의 노드가 deque들에 들어가게 됨.
따라서 O(V + E)가 됨.

adj Matrix를 사용한 경우, V^2인 2차원 배열을 사용하게 된다. 중복된 E가 많아질 수 있는 euler path의 특성상 adj Matrix가 유효할 수 있음.

# Summary

- 무향 그래프에서 오일러 경로의 존재성을 판단하는 문제 : [[16168. 퍼레이드]]
- 무향 그래프에서 오일러 회로를 구하는 문제 : 1199. 오일러 회로