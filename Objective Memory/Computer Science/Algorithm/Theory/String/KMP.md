---
Date: 2024-08-01
tags:
---
# Prerequisite Algorithm
# Concept
- 큰 문자열 속에서 작은 문자열(패턴)을 찾아내는, 패턴 매칭 알고리즘.
- 알고리즘의 고안자인 세 명 Knuth, Moris, Pratt의 이름을 따서 KMP
# Implementation

## Code

``` C++
#include <string>
#include <vector>

class KMP
{
private:
	std::vector<int> t;
	std::string_view s, w;
	KMP();

public:
	// failure function
	// preprocessing, build partial match table
	KMP(std::string_view heystack, std::string_view needle) : s(heystack), w(needle), t(needle.size() + 1, 0)
	{
		int pos = 1, cnd = 0; // pos : current position of table to fill
		// cnd : next char of current candidate substring

// cnd points at previous position that can be compared.
// cnd also means length of maximum prefix
		t[0] = -1;
		while (pos < w.size())
		{
			if (w[pos] == w[cnd]) // match
			{
				t[pos] = t[cnd];
			}
			else // mismatch
			{
				t[pos] = cnd;
				while (0 <= cnd && w[pos] != w[cnd]) // fall back during mismatch
					cnd = t[cnd];					 // cmd == -1 means no going back
			}
			pos++;
			cnd++;
		}
		t[pos] = cnd; // ???
	}
	// search function
	// search, positions that "needle" matched int "heystack"
	std::vector<int> operator()()
	{
		std::vector<int> result;
		auto sIdx = 0, wIdx = 0;

		while (sIdx < s.size()) // O( length of s )
		{
			if (s[sIdx] == w[wIdx]) // match
			{
				sIdx++;
				wIdx++;
				if (wIdx == w.size()) // occurence found
				{
					result.push_back(sIdx - wIdx);
					wIdx = t[wIdx]; // not -1
				}
			}
			else // mis match, jump wIdx back
			{
				wIdx = t[wIdx];
				if (wIdx < 0) // -1 means there is no going back
				{
					sIdx++;
					wIdx++;
				}
			}
		}
		return result;
	}
};
```

## About Code

KMP 알고리즘은 두 개의 함수로 구성된다. 
```C++
// failure function


```
failure function은 탐색의 대상이 되는 패턴 문자열에 대한 전처리를 수행하는 함수이다. 실행의 결과로 테이블을 초기화 하는데, 이 테이블은 비교에서 실패할 경우 다시 비교를 시작할 위치를 저장한다. 이 테이블을 활용하여, 탐색 과정에서 발생하는 두 문자열 사이의 비교에서 중복 비교를 제거할 수 있다.

```C++
// search function

```
# Analysis

## Time Complexity

## Spatial Complexity

# Summary
