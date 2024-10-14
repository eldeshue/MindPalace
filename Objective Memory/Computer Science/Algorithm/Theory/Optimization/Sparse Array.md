---
Date: 2024-10-14
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
// 희소배열 초기화
// 정의역, x의 범위 1 ~ N
std::vector<std::vector<int>> f(log2(N) + 2, std::vector<int>(N + 1));
void initFn()
{
	for (int n = 1; n <= N; ++n)
	{
		// f(n) 초기화
		f[0][n] = ... ;
	}
	for (int pow = 1; pos <= log2(N) + 1; ++pow)
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
	for (int pos = log2(N) + 1; pos >= 0; --pos)// pos of bit
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

## Time Complexity

## Spatial Complexity

# Summary
