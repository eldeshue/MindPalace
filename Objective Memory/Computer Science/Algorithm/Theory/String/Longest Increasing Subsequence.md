---
Date: 2024-10-14
tags:
---
# Prerequisite Algorithm
[[Binary Search]], [[Array]]
# Concept

## Subsequence?
부분 수열(subsequence)은 어느 수열(배열)의 일부로, 원본이 되는 수열의 순서를 따른다. 부분 문자열(substring)과 유사한데, 부분 문자열은 그 원소의 순서에 더해서 연속성이 보장되어야 하고, 부분 수열은 순서만 보장한다. 따라서 부분 문자열의 집합은 부분 수열의 집합의 부분 집합이다.

## LIS
증가하는 부분 수열(Increasing subsequence)는 수열을 이루는 원소가 증가하는 순서를 이루는 것으로, LIS는 원본 수열에서 찾을 수 있는 부분 수열 중, 그 길이가 가장 긴 것이다.

# Implementation

## Code

``` C++
// size N, int array
// std::vector<int> nums;

// sollution 1 : O( N ^ 2 )
int getLISLen1(const std::vector<int> &nums)
{
	const int N = nums.size();
	std::vector<int> lenOfLis(N, 1);
	int result = 1;
	for (int i = 1; i < N; ++i)
	{
		const int &curVal = nums[i];
		int &curLen = lenOfLis[i];
		int prevMaxLen = 0;
		for (int j = 0; j < i; ++j)
		{
			const int &prevVal = nums[j];
			const int &prevLen = lenOfLis[j];
			if (curVal > prevVal)
			{
				prevMaxLen = std::max(prevMaxLen, prevLen);
			}
		}
		curLen = prevLen + 1;
		result = std::max(result, curLen);
	}
	return result;
}

// solution 2 : O( N log N )
int getLISLen2(const std::vector<int> &nums)
{
	const int N = nums.size();
	std::vector<int> minValPerLen(N, 2e9); // save minimum value per lenght of IS
    int result = 0;                            // index is length

	minValPerLen[0] = 0; // minimum value
	for (int i = 0; i < N; ++i)
	{
		const int &curVal = nums[i];
		// binary serach, find first IS with lenght that last value is greater than curVal.
		auto targetPos = std::lower_bound(minValPerLen.begin(), minValPerLen.end(), curVal);
		*targetPos = curVal;
        result = std::max(result, static_cast<int>(targetPos - minValPerLen.begin()));
	}
	return result;
}

```

## About Code

## Naive, N ^ 2

``` C++
int getLISLen1(const std::vector<int> &nums)
{
	const int N = nums.size();
	std::vector<int> lenOfLis(N, 1); // nums[i]를 끝으로 하는 LIS의 길이를 저장
	int result = 1;
	for (int i = 1; i < N; ++i)
	{
		const int &curVal = nums[i];
		int &curLen = lenOfLis[i];
		int prevMaxLen = 0;
		for (int j = 0; j < i; ++j)
		{
			const int &prevVal = nums[j];
			const int &prevLen = lenOfLis[j];
			if (curVal > prevVal) // nums[i]보다 작으면서, 그 LIS 길이는 최대인 j 찾기
			{
				prevMaxLen = std::max(prevMaxLen, prevLen);
			}
		}
		curLen = prevLen + 1; // 찾은 최대 길이를 바탕으로 nums[i]를 끝으로 하는 LIS 업데이트
		result = std::max(result, curLen);
	}
	return result;
}
```
i번째 원소(``nums[i]``)를 끝으로 하는 LIS의 길이(``lenOfLis[i]``)를 구하기 위해서 앞서 구한, 즉 j < i인 j에 대하여 ``nums[i] > nums[j]``를 만족하는 것 중, ``lenOfLis[j]``의 최댓값을 찾는다.
## Optimized, N log N

``` C++
int getLISLen2(const std::vector<int> &nums)
{
	const int N = nums.size();
	std::vector<int> minLastValPerLen(N, 2e9); // save minimum value per lenght of IS
    int result = 0;                            // index is length

	minLastValPerLen[0] = 0; // minimum value
	for (int i = 0; i < N; ++i)
	{
		const int &curVal = nums[i];
		// binary serach, find first IS with lenght that last value is greater than curVal.
		auto targetPos = std::lower_bound(minLastValPerLen.begin(), minLastValPerLen.end(), curVal);
		*targetPos = curVal;
        result = std::max(result, static_cast<int>(targetPos - minLastValPerLen.begin()));
	}
	return result;
}
```
앞서 구한 알고리즘을 2진 탐색을 이용하여 개선한다. 2진 탐색을 적용하기 위해서는 탐색의 대상이 단조성을 유지해야 한다. 이러한 단조성을 보장하기 위해서 ``minLastValPerLen``을 도입한다.

``minLastValPerLen[l]``은 길이가 ``l``인 LIS에 대하여, 최소인 마지막 원소를 의미한다. 따라서 이진 탐색으로 ``nums[i]``이상인 첫 번째 ``minLastValPerLen[l]``을 구하면, 곧 ``l``이 ``nums[i]``를 끝으로 하는 LIS의 길이가 된다.

중요한 점은 ``minLastValPerLen``이 항상 증가하는 상태를 유지해야 함에 있는데, 이는 귀류법적인 논리로 이해될 수 있다.
> > **가정** : ``i < j``에 대하여, ``minLastValPerLen[i] > minLastValPerLen[j]`` 이다.
> 
> 길이가 ``i``인 LIS의 마지막 원소가 길이가 ``j``인 LIS의 마지막 값보다 크다.
> 
> 이는 ``minLastValPerLen[j]`` 를 끝으로 하는 부분 수열이 확장 가능함을 의미하며, 이는 곧 LIS의 정의, 즉 longest라는 전제에 위배된다.
>
> > 전제 : LIS는 **가장 긴** 증가하는 부분 수열이다.
>
>즉 모순이 발견되었으므로, 해당 가정은 거짓이다.

따라서 minLastValPerLen은 항상 정렬된 상태를 유지하며, 이진 탐색 가능하고, 알고리즘은 정당하다.
# Summary
- 대부분의 경우 N^2 풀이는 큰 의미가 없고, n log n 풀이가 중요함.
- 2진 탐색의 우아한 응용.
- 꽤 많은 문자열 및 배열 문제가 해당 알고리즘에 기반하기에, 대략적으로는 외워주면 좋음.
- 알고리즘의 핵심은 길이 별 LIS에 대하여 마지막 값의 최솟값을 유지하는 데에 있음.