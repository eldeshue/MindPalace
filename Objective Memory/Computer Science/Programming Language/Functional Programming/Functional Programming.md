---
Date: 2024-10-17
---
# 함수형 프로그래밍이란?

함수형 프로그래밍이란 프로그래밍 방법론의 하나로, 함수를 중심으로 논리를 전개한다.

## 함수란 무엇이었나?

기존의 프로그래밍 환경에서 함수란 코드, 즉 데이터를 가공하는 일련의 행위를 기록한 것이다. 호출(call)을 통해서 실행만되며, 조작은 불가능한, 일반적인 데이터와 엄격하게 구분되는 대상이었다.

함수, 즉 code는 성역이었다.
## 함수도 데이터다.

함수형 프로그래밍에서 함수는 더 이상 특별하지 않다. 함수는 그저 데이터다. 호출이라는 특이한 연산이 가능할 뿐이며, 호출은 뒤늦은 평가evaluation에 불과하다. 심지어 함수형 프로그래밍이 가능한 언어에서 함수는 전달, 복사, 대입 등 기본적인 연산의 대상이 된다. 

## 모든 것이 함수다.

오히려 기존에 다루던 데이터를 함수로 이해할 수 있다.

- 리터럴
	각종 리터럴(int, float, double)등은 인자를 받지 않는 상수 함수이다. class는 여러 상수 함수(field)와 함수(method)로 구성된, 일종의 구조체가 된다
- 배열
	인덱스를 인자로 받고, 해당 위치에 존재하는 값을 반환하는 함수이다.
- 해시맵
	key값을 인자로 받고, 해당 key에 연관된 값을 반환하는 함수이다.

## 장점
- 합성 및 커링을 통한 코드의 재사용성의 극대화
	- 중복된 코드 제거, 메모리 절감
	- 동일한 로직을 재활용, 같은 에러가 여러번 등장하지 않음
	- 중복 코드를 작성하지 않으므로, 높은 생산성
- [[Pure Function | 함수의 순수성]]에서 오는 여러 장점(병렬 친화성, 비동기 친화성, ...)
-  lazy evaluatioin : currying을 통해서 값을 전달만 하고 실행을 미룰 수 있다. 따라서 lazy evaluation이 가능하다.
- ...

## 단점
- 소통의 난해함
	- 함수형은 전통적인 방식에 비해서 이질적, 함수형을 이해하는 동료가 적음.
- 부족한 정보
	- 이미 작성된 대부분의 코드는 객체지향, 구조적 프로그래밍에 기반함. 
	- 대부분의 알고리즘이 오토마타(상태기계) 기반, 함수형이 아님.
- 순수성의 까다로운 조건
	- 복사 : 할당(assign)하는 행위는 함수를 변조하는 행위로, 이는 순수성에 위배된다.
	- ...