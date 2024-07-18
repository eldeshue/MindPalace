---
Date: 2024-07-17
tags:
---
# Prerequisite Algorithm
[[Sorting]]
# Concept
- **Non Linear** : 기존의 선형 탐색, 즉 모든 데이터를 일일이 검사하는 완전탐색의 경우, 탐색 시간이 원소의 양에 선형으로 비례함. 그러나, 주어진 데이터가 정렬되어 있다면, 그 순차성을 바탕으로 불필요한 탐색을 제거, 비선형적 탐색으로 답을 구할 수 있음.

- **Mid Value** : 현재 탐색 범위의 **중앙값**을 기준으로, 탐색하고자 하는 대상은 이분 된 두 파티션 중 하나에 존재함. 따라서 한 번에 절반의 탐색 범위를 제거할 수 있음. 
 
- **Comparability** : 다만, 이는 주어진 원소가 **정렬 가능**해야 함. 즉 다시 말하면, 원소 사이에 **일관된 비교**가 가능해야 함.

# Implementation

## Code

이진탐색의 구현은 대략 다음과 같다.

``` C++
// from start to end, search int value n's position
// [startIdx, endIdx], both included
int binarySearch(const std::vector<int> &vec, int startIdx, int endIdx, int val)
{
	int left = startIdx;
	int right = endIdx;
	while (left < right)
	{
		int mid = (left + right) / 2;
		if (vec[mid] > val) // not found, shrink range into half
		{
			right = mid - 1;
		}
		else if (vec[mid] == val) // found
		{
			return mid;
		}
		else   // vec[mid] < val, shrink range into half
		{
			left = mid + 1;
		}
	}
	return -1;    // not found
}
```

c++ std라이브러리 중 algorithm에는 이진 탐색 함수가 존재하며, 이는 다음과 같다.

```C++
#include <algorithm>

template <class ForwardIt, class T, class Compare>
bool binary_search(ForwardIt first, ForwardIt last, const T& value, Compare comp);
```

- 탐색 범위 : 정방향 순회 Iterator, [ first, last  )
- 탐색 대상 : value
- 비교 기준 : 함수객체, comparator. range를 좁히기 위해 비교대상(elem)과 탐색대상(value)를 비교함. 비교 결과가 false이면, 즉 element < value가 왼쪽에서 참을, 오른 쪽에서 거짓을 반환해야 함. 즉 elem이 **오름차순**으로 정렬되어야 함. 

# Analysis

## Time Complexity - O(log n)
 
 탐색의 대상이 되는 자료구조의 크기를 n이라 하고, 해당 자료구조에 **랜덤하게 접근가능**하다면, 탐색의 시간 복잡도는 **log n**이다. 그 이유는 한 번의 loop를 거칠 때 마다 탐색 대상 범위가 1/2로 축소되기 때문이다.

다만, 이진탐색의 선결 조건이 정렬이므로, 대부분의 경우 정렬에 드는 비용이 dominant하다.
## Spatial Complexity - O(1)

**탐색 대상의 크기(n)와는 별개**로, 3개의 변수만 있으면 되므로, 공간 복잡도는 **상수**이다.
# Summary

- 직관적인 선형 알고리즘의 틀에서 벗어나, 본격적으로 log n의 강력함을 보여주는 관문.
- PS에서는 이진탐색 그 자체로는 잘 사용하지 않고, 특정 조건과 함께 동작하는 [[Parametric Search]]의 형태로 자주 등장한다.

