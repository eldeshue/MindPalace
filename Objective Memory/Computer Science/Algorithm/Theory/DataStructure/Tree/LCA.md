---
Date: 2024-10-08
tags:
---
# Prerequisite Algorithm
[[Tree]], [[Sparse Table]], [[GraphSearch|DFS]]
# Concept
- 트리에서 최단 공통조상(Least Common Ancestor, LCA)를 구하는 알고리즘.
- 최단 공통조상이란 트리에서 두 노드의 공통조상 중, 가장 깊은(혹은 두 노드에서 가장 가까운) 노드를 의미함.
- 다음의 과정을 거침. 
	1) 두 노드를 가리키는 커서를 준비함. 커서는 포인터, 인덱스 어느 것도 좋음.
	2) 두 커서가 가리키는 노드의 깊이가 같아질 때 까지 어느 한 쪽 커서를 부모를 향해 이동, 균형을 맞춤.
	3) 두 커서가 가리키는 노드의 depth가 같아졌다면, 두 커서를 동시에  이동.
	4) 두 커서가 가리키는 노드가 같아지는 순간, 이 노드가 LCA 노드임. 
- 희소배열을 통해서 커서를 이동시키는 과정을 단축.
# Implementation

## Code

``` C++

#include <vector>
#include <queue>

using ll = long long;
using pii = std::pair<int, int>; // child node, cost
using Graph = std::vector<std::vector<pii>>;
class LCATree
{
private:
	static constexpr int ROOT = 1;

	// field
	Graph const &graph;
	int const size; // size of nodes, N
	int const lgSize;
	std::vector<std::vector<int>> sparseTable;
	std::vector<int> &parent; // ref to sparseTable[0]
	std::vector<int> depth;

	LCATree();
	LCATree(const LCATree &);
	LCATree &operator=(const LCATree &);

	int log2(int n)
	{
		int bitCnt = 0;
		while (n > 1)
		{
			n >>= 1;
			bitCnt++;
		}
		return bitCnt;
	}

	void bfs()
	{
		std::queue<int> nextNodes;
		std::vector<bool> visited(size + 1, false);

		visited[ROOT] = true;
		nextNodes.push(ROOT);
		while (!nextNodes.empty())
		{
			int curNode = nextNodes.front();
			nextNodes.pop();
			for (const auto [childNode, cost] : graph[curNode])
			{
				if (visited[childNode] == false)
				{
					visited[childNode] = true;
					parent[childNode] = curNode;
					depth[childNode] = depth[curNode] + 1;
					nextNodes.push(childNode);
				}
			}
		}
	}

public:
	LCATree(const int N, const Graph &g) : size(N), lgSize(log2(N) + 1), sparseTable(lgSize + 1, std::vector<int>(size + 1, 0)),
										   graph(g), parent(sparseTable[0]), depth(N + 1, 0)
	{
		// setting parent and dist
		bfs();

		// setting sparse table
		for (int i = 1; i <= lgSize; ++i)
			for (int j = 1; j <= size; ++j)
				sparseTable[i][j] = sparseTable[i - 1][sparseTable[i - 1][j]];
	}
	~LCATree() {}

	int queryLCA(int node1, int node2)
	{
		const int d1 = depth[node1];// depth of node1
		const int d2 = depth[node2];// depth of node2
		if (d1 != d2)// balance the depth of two node
		{
			// move deeper one up, make d1 and d2 same
			int &deeperNode = (d1 > d2) ? node1 : node2;
			int diff = std::abs(d1 - d2);
			for (int pos = 0; pos <= lgSize; ++pos)
			{
				if (diff & (1 << pos))
				{
					deeperNode = sparseTable[pos][deeperNode];
				}
			}
		}
		// search, lower bound, log N, jump
		int lcaNode = node1;
		if (node1 != node2)
		{
			for (int pos = lgSize; pos >= 0; --pos)
			{
				const int nextNode1 = sparseTable[pos][node1];
				const int nextNode2 = sparseTable[pos][node2];
				if (nextNode1 != nextNode2)
				{
					node1 = nextNode1;
					node2 = nextNode2;
				}
			}
			lcaNode = sparseTable[0][node1];
		}
		return lcaNode;
	}

};

```

## About Code

# Analysis

## Spatial Complexity - O (N log N)
트리에서 root를 향해 거슬러 오르는 과정을 최적화 하기 위해서 sparse table을 활용한다. 최악의 경우는 트리가 일렬로 늘어진 모양으로, 그 depth가 최대 N이 되며, 이는 곧 최대 반복 횟수로 N이다. 따라서 sparse table에 의해서 O(N log N)이 된다.

## Time Complexity - O(N log N), O ( log N)
``` C++
// init sparsetTree
LCATree(const int N, const Graph &g) : size(N), lgSize(log2(N) + 1), sparseTable(lgSize + 1, std::vector<int>(size + 1, 0)),
										   graph(g), parent(sparseTable[0]), depth(N + 1, 0)
	{
		// setting parent and dist
		bfs();

		// setting sparse table
		for (int i = 1; i <= lgSize; ++i)
			for (int j = 1; j <= size; ++j)
				sparseTable[i][j] = sparseTable[i - 1][sparseTable[i - 1][j]];
	}
```
앞서 밝힌 바에 따라서 sparse table을 초기화 하므로, 그 비용은 O(N log N)이다. 비록 부모 노드를 기록하기 위해서 bfs를 1회 수행하지만, 이는 dominant하지 않으므로 생략된다.

``` C++
	// find LCA node of node1 and node2
	int queryLCA(int node1, int node2)
	{
		const int d1 = depth[node1];// depth of node1
		const int d2 = depth[node2];// depth of node2
		if (d1 != d2)// balance the depth of two node
		{
			// move deeper one up, make d1 and d2 same
			int &deeperNode = (d1 > d2) ? node1 : node2;
			int diff = std::abs(d1 - d2);
			for (int pos = 0; pos <= lgSize; ++pos)
			{
				if (diff & (1 << pos))
				{
					deeperNode = sparseTable[pos][deeperNode];
				}
			}
		}
		// search, lower bound, log N, jump
		int lcaNode = node1;
		if (node1 != node2)
		{
			for (int pos = lgSize; pos >= 0; --pos) // log N
			{
				const int nextNode1 = sparseTable[pos][node1];
				const int nextNode2 = sparseTable[pos][node2];
				// move together until different
				if (nextNode1 != nextNode2)
				{
					node1 = nextNode1;
					node2 = nextNode2;
				}
			}
			lcaNode = sparseTable[0][node1];
		}
		return lcaNode;
	}
```

부모노드를 향해서 커서를 움직이는 과정을 희소배열로 최적화함. 커서를 움직이는 과정을 일종의 변환으로 생각할 수 있음. 즉  ``부모노드 == f(자식노드)``인 변환 f라 하면, 현재 노드에서 root를 향해서 n번 이동한 노드는 변환 f를 n번 합성한 f_n이라 할 수 있음. 따라서 해당 과정은 sparse table로 최적화 가능함.

최악의 경우는 모든 노드가 하나의 자식을 갖는 경우이다. 따라서 시간 복잡도는 O(N)이며, 이를 희소배열로 최적화 할 경우, O( log N )이다.
# Summary

LCA를 한 번만 구하는 문제의 경우 최적화를 수행하지 않는 경우가 더 좋지만, 하나의 트리에 대해서 LCA를 여러번 물어보는 일종의 query문제에 대해서는 해당 풀이를 수행해야 함.