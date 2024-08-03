---
Date: 2024-08-03
tags:
---
# Prerequisite Algorithm
[[DP]] , [[Two-Pointer]]
# Concept
- 주어진 배열에서 sub-array의 합의 최댓값을 구하는 알고리즘.
- sub-array는 배열의 연속된 부분을 의미함.
- Optimized DP.
# Implementation

## Code

``` C++
// BOJ 1912
// Kadane's algorithm을 이용한 풀이
#include <iostream>
#include <vector>

int main()
{
	std::ios_base::sync_with_stdio(false);
	std::cin.tie(nullptr);
	std::cout.tie(nullptr);

	int N, maxSum = -2e9, curSum = 0;
	std::cin >> N;
	std::vector<int> nums(N); 
	for (int i = 0; i < N; ++i)				// init array
	{
		std::cin >> nums[i];
	}
	for (int i = 0; i < N; ++i)				// O(N)
	{
		curSum += nums[i];					// 합을 누적
		maxSum = std::max(maxSum, curSum);	// 최댓값 업데이트
		if (curSum < 0)						// 기존의 누적 합을 버림.
			curSum = 0;
	}
	std::cout << maxSum;
}
```
- ``N`` : 배열의 크기
- ``nums`` : 원소를 저장한 배열.
- ``curSum`` : 현재 원소들의 누적 합.
- ``maxSum`` : 최대 누적 합.

## About Code
### 실행 과정
1. 먼저 배열을 순회하면서 각 배열의 원소를 ``curSum``에 누적(accumulate)한다.
2. ``curSum``을 ``maxSum``과 비교하여 maxSum을 업데이트 한다.
3. 만약 ``curSum``이 0보다 작으면, 그 값을 0으로 초기화 한다.
\
### 풀이 설명 - 어째서 ``curSum``을 초기화 하는가?

``curSum``이 감소하는 한이 있더라도, 양수라면 최대 누적합의 일부가 될 가능성이 있다. 
하지만, ``curSum``이 음수가 되는 순간, 이를 유지하느니 새롭게 시작하는게 항상 이득이 된다.
즉, 어떤 연속된 누적합의 일부도 음수가 되어서는 최댓값이 될 수 없다.
# Analysis

## Time Complexity - O(N)
배열을 one-pass로 순회하는 것으로 문제를 해결하므로, 그 복잡도는 O(N)이다.

## Spatial Complexity - O(1)
원소를 저장할 배열을 제외하면, 별도의 메모리를 필요로 하지 않는다.

# Summary

검색 결과, 많은 글들이 해당 풀이를 DP의 최적화로 설명하고, 또 그러한 부분에 동의한다. 하지만 내가 1912번을 풀면서 해당 풀이를 도출한 과정은 오히려 two-pointer에서 시작했다.

최초에 생각했던 풀이는 다음과 같다. 먼저, sub-array의 시작과 끝을 가리키는 두 개의 포인터를 생각했다. 끝을 가리키는 오른쪽 포인터를 진행시키며 누적합을 계산해나간다. 각 누적합에 대해서 최댓값을 갱신하는데, 만약 누적합이 음수가 되면, 두 포인터 모두를 끝을 가리키는 포인터의 다음 위치로 옯긴다. **즉 지금까지 얻은 누적합(``curSum``)이 최대 누적합(``maxSum``)에 도움이 안되면, 이를 버리는 것이 항상 옳다.**

>> **누적합이 도움이 되는 동안에는 유지하고, 그렇지 않으면 버린다.**

나는 이렇게 투 포인터에서 생각을 발전시켜 해당 풀이에 도달했는데, 사실은 Kadane's algorithm이라는 유명한 테크닉이었다. 만약 문제에서 요구한 것이 최댓값에 더해서 최댓값이 나오는 sub-array를 구하는 것이었다면, two-pointer였으리라.