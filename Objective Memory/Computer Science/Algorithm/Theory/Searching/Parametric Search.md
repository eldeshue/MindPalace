---
Date: 2024-07-18
tags:
---
# Prerequisite Algorithm
[[Binary Search]], [[Sorting]]
# Concept
- Binary Search : 기본적으로는 이진 탐색.
- Contiguous, bipartite Range of Conditions : 특정 조건을 만족하는 범위가 탐색 범위 내에서 연속적으로 존재해야 하며, 탐색범위는 이분되어야 함.
- Range Max/Min : 위 두 조건을 만족하는 상황에 대해서, parametric search 알고리즘은 이분탐색을 활용하여 목표로 하는 범위의 경곗값을 구함. 목표로 하는 범위가 left라면 max를, right라 하면 min값을 구할 수 있음. 범위 포함 판정을 위한 bool식이 필요함.
# Implementation

## Code

``` C++
// in range minimum, return index of the minimum
// sorting required
template<typename T>
int parametricSearchMin(const std::vector<T> &vec, int minIdx, int maxIdx, std::function<bool(const T&)> isOk)
{
	int left = minIdx, right = maxIdx + 1; // half open range
	while (left < right)
	{
		int mid = (left + right) >> 1; // divide by 2
		if (isOk(vec[mid]))
		{
			// true, in range
			right = mid;
		}
		else
		{
			// false, out of range
			left = mid + 1;
		}
	}
	if (left == maxIdx + 1) // search fails
	{
		return -1;
	}
	return left;
}

// range max
// sorting required
template<typename T>
int parametricSearchMax(const std::vector<T> &vec, int minIdx, int maxIdx, std::function<bool(const T&)> isOk)
{
	int left = minIdx - 1, right = maxIdx; // half open range
	while (left < right)
	{
		int mid = (left + right + 1) >> 1; // divide by 2
		if (isOk(vec[mid]))
		{
			// true, in range
			left = mid;
		}
		else
		{
			// false, out of range
			right = mid - 1;
		}
	}
	if (right == minIdx - 1) // search fails
	{
		return -1;
	}
	return rhgit;
}

```
위 코드는 vector 내에서 조건을 만족하는(즉 isOk가 참을 반환하는)원소에 대해서 그 위치를 찾는 함수다. 일반적으로 binary search를 하는 경우는 그 range가 매우 큰 경우이므로, 보통 int, float 등 숫자값 자체를 탐색의 대상(즉 T가 int가 되는 경우)으로 삼는 경우가 더 흔하다.

> **integer 그 자체를 탐색의 대상으로 삼는 경우, left + right에서 overflow가 발생할 수 있다.**

한 편, c++ 표준 라이브러리에는 upper_bound함수와 lower_bound함수가 있어서 parametric_search 상황에 간편하게 사용할 수 있다.
```C++
#include <algorithm>

template <class ForwardIt, class T, class Compare>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value,
                      Compare comp);
ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value,
                      Compare comp);
```
\[ first, end ) 의 half open range에 대하여...
lower_bound는 
# Analysis

## Time Complexity - O(k log n)

이분탐색의 응용이므로, 기본적으로 이진탐색의 비용을 따라가므로 O(log n)이다. 다만, isOk함수, 즉 조건을 판별하는 과정에서 발생하는 비용 O(k)를 고려하면 O(k log n)이라 할 수 있다.
## Spatial Complexity - O(1)

마찬가지로, 이분탐색의 비용을 따라가므로 O(1)이다.

# Summary

- reference from [palilo of BOJ](https://www.acmicpc.net/source/share/2e8b21f265ec49eeae1aecbbf73a2db1)
