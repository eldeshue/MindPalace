---
Date: 2024-08-01
tags:
---
# Prerequisite Algorithm
# Concept
- 큰 문자열,text 속에서 작은 문자열,pattern을 찾아내는, pattern-matching 알고리즘.
- Naive 알고리즘에서 활용하지 않던, **이미 탐색한 정보를 재활용**한다.
- 알고리즘의 발명자 Knuth, Moris, Pratt의 이름을 따서 KMP라 부른다.
# Implementation

## Visualize
// 여기에 이미지 삽입
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
		int pos = 1; // pos : current position of table to fill
		int cnd = 0; // cnd : next char of current candidate substring

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
					cnd = t[cnd];					 // cmd == -1 means no go back
			}
			pos++;
			cnd++;
		}
		t[pos] = cnd; // pos == w.size()
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
- ``t`` : prefix jump table, failure function의 결과물, 패턴 중 이미 찾아낸 prefix에 대한 부분을 skip.
- ``s`` : heystack의 string_view. pattern을 찾아내야 하는 원본 text. 
- ``w`` : needle의 string_view. text 안에서 찾아내야 하는 pattern.
## About Code

KMP 알고리즘은 두 개의 함수로 구성된다. 
### Failure Function
```C++
// failure function
KMP(std::string_view heystack, std::string_view needle) : s(heystack), w(needle), t(needle.size() + 1, 0)
{
	int pos = 1; // pos : current position of table to fill
	int cnd = 0; // cnd : next char of current candidate substring

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
				cnd = t[cnd];					 // cmd == -1 means no go back
		}
		pos++;
		cnd++;
	}
	t[pos] = cnd; // ???
}
```
kmp를 수행하기 위해서는 먼저 failure function을 수행하여 table ``t``를 초기화해야 한다.  이 테이블은 비교에서 mismatch가 발생할 경우 다시 비교를 시작할 위치를 저장한다. 이 테이블을 활용하여, 탐색 과정에서 발생하는 두 문자열 ``w``와 ``s`` 사이의 비교에서 중복 비교를 제거할 수 있다.

테이블을 초기화 하는 과정은 패턴  **w에 대해서 w자신과 비교를 수행**하여 **w안에서 자신의 prefix를 찾는다.** prefix를 찾으면, 그 prefix가 끝나는 위치를 테이블에 기록한다. 이를 통해서 탐색 중 ``s``와 ``w``사이의 mismatch가 발생했을 때, w의 비교 위치(인덱스)를 t에 저장된 값으로 바꾸는 것으로, 공통으로 갖는 prefix에 대한 탐색은 skip한다.

>> **Failure Function은 자기 자신 안에서 스스로를 찾는 과정이다.**

---
### Search Function
```C++
// search function
std::vector<int> operator()()
{
	std::vector<int> result;  // 탐색 결과, 발견한 w의 시작 위치를 저장
	auto sIdx = 0, wIdx = 0;

	while (sIdx < s.size()) // 매 s에 대해서 w와 비교
	{
		if (s[sIdx] == w[wIdx]) // 일치하는 경우, 탐색을 지속함
		{
			sIdx++;
			wIdx++;
			if (wIdx == w.size()) // w의 끝까지 탐색 완료, 패턴을 찾았음.
			{
				result.push_back(sIdx - wIdx); // 결과 저장
				wIdx = t[wIdx];                // t에 저장된 재탐색 위치로 이동
			}
		}
		else // 불일치 발생, t를 참조하여 재탐색 위치로 이동
		{
			wIdx = t[wIdx]; // 재탐색 위치로 이동
			if (wIdx < 0)   // 재탐색 위치가 -1인 경우, prefix가 없음. 처음부터 탐색
			{
				sIdx++;
				wIdx++;
			}
		}
	}
	return result;
}
```
앞서 획득한 ``t``를 이용하여 ``s``속에서 ``w``를 찾는다. Naive한 패턴 매칭 알고리즘에서는 s의 매 문자마다 w를 비교했다. 비교 중 mismatching이 발생하면 다시 w의 처음부터 비교를 수행했다. KMP는 ``t``테이블을 이용하여 다시 처음부터 비교하는 것이 아닌, **잠재적으로 일치하는 부분 이후부터 비교**를 수행한다.

>> **Search Function은 세상 속에서 스스로를 찾는 과정이다.**

---
# Analysis
패턴의 길이를 N, 텍스트의 길이를 M.
## Time Complexity

- Failure Function : O(N)
- Search Function : O(N + M)
	-> naive 알고리즘의 복잡도가 O(N * M)이었음을 감안하면, 대단한 발전.
## Spatial Complexity - O(N)
mismatch 발생 시 참조할 테이블 ``t``를 위해서 공간이 필요함.
# Summary

KMP는 **지피지기면 백전백승**의 좋은 예시라 할 수 있다. failure function을 통해서 자신(패턴)에 대한 이해를 얻고(지피지기), 자신에 대한 이해를 바탕으로 타인(소스) 속에서 나 자신을 찾아내는 과정이기 때문이다. 이러한 철학적인 함의 탓에, 나는 KMP가 대단히 우아하고, 또 훌륭한 알고리즘이라 생각한다.