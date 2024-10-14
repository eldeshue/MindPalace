---
Date: 2024-10-07
tags:
---
# Prerequisite Algorithm
이진수, 결합법칙, 함수의 합성
# Concept
- 어떤 작업 f에 대하여, N번 반복수행을 O(N)에서 O(log N)으로 줄이는 최적화 테크닉.
- N을 2의 거듭제곱의 합으로 분해, 이들의 결과를 미리 저장하고, 이들의 조합으로 답을 구함.
- ex) f_2(x) = f(f(x)), f_3(x) = f(f(f(x))), ... f_N(x) = f(f(...f(x)...))에 대하여 f_30(x)을 구하는 경우
		30을 2진수로 표현하면, 11110이므로, f_30(x) = f_16(f_8(f_4(f_2(x))))이다.
- 희소배열 테크닉을 적용하기 위해서는 결합법칙이 성립해야 함.
- 추가적인 메모리를 투입하여 작업 결과를 저장하고, 이를 재활용하는 점에서 이득이 발생하므로, 일련의 쿼리를 처리해야 하는 경우에 대단히 유용함.
# Implementation

## Code

``` C++
// 반복횟수의 범위 1 ~ M
// 정의역, x의 범위 1 ~ N
// 희소배열 초기화
std::vector<std::vector<int>> f(log2(M) + 2, std::vector<int>(N + 1));
void initFn()
{
	for (int n = 1; n <= N; ++n)
	{
		// f(n) 초기화
		f[0][n] = ... ;
	}
	for (int pow = 1; pos <= log2(M) + 1; ++pow)
	{
		for (int n = 1; n <= N; ++n)
		{
			// f_x(n) = f_x/2( f_x/2( n ) );
			f[pow][n] = f[pow - 1][f[pow - 1][n]];
		}
	}
}

// 희소배열 조합
int query(int x, int step)
{
	for (int pos = log2(M) + 1; pos >= 0; --pos)// pos of bit to apply
	{
		if (step & (1 << pos))
		{
			x = f[pos][x];
		}
	}
	return x;
}

```

## About Code

# Analysis

## Time Complexity - O(N log M), O( log M )

``` C++
void initFn()
{
	for (int n = 1; n <= N; ++n)
	{
		// f(n) 초기화
		f[0][n] = ... ;
	}
	for (int pow = 1; pos <= log2(M) + 1; ++pow)
	{
		for (int n = 1; n <= N; ++n)
		{
			// f_x(n) = f_x/2( f_x/2( n ) );
			f[pow][n] = f[pow - 1][f[pow - 1][n]];
		}
	}
}
```
희소배열을 초기화 하는 과정에서 N log M회의 연산이 사용된다. 

``` C++
// 희소배열 조합
int query(int x, int step)
{
	for (int pos = log2(M) + 1; pos >= 0; --pos)// pos of bit
	{
		if (step & (1 << pos))
		{
			x = f[pos][x];
		}
	}
	return x;
}
```
쿼리를 처리하는 과정에서 앞서 초기화한 희소배열을 재활용한다. 재활용 과정의 경우, 반복횟수의 비트마스크를 확인하고, 이를 조합하는 과정을 거치므로 log (M)의 비용이 든다.
## Spatial Complexity - O( N log  M )
``` C++
// 희소배열 초기화
// 반복횟수의 범위 1 ~ M
// 정의역, x의 범위 1 ~ N
std::vector<std::vector<int>> f(log2(M) + 2, std::vector<int>(N + 1));
```
희소배열에 분해한 2의 거듭제곱회 반복에 대한 결과 값을 저장해야 한다. 따라서 N log M의 메모리가 필요하다. 

---
# Summary

희소배열의 장점은, 일정한 초기투자로 여러 질문의 비용을 절약함에 있다. 총 K개의 쿼리를 처리한다고 할 때, **단순 반복으로 구하면 O(K * M)이지만, 희소배열을 활용하면 O(N log M + K log M)이다.** 따라서 문제상황에 대한 심도깊은 분석(즉 문제가 쿼리의 처리를 요구하는지 등)이 선행되어야 한다.

또한 메모리는 유한하므로, 최적화의 대상이 되는 변환 f의 정의역의 크기(즉 N)에 대한 고려가 필요하다.

희소배열 테크닉의 경우 단독으로는 잘 등장하지 않고, 대부분의 경우 [[Tree|트리]]에서 [[LCA]](최소 공통조상)와 함께 등장한다.
