---
Date: 2024-11-19
---
# Overview

소켓 함수는 네트워크 프로그래밍을 위해서 소켓 객체를 생성하는 함수이다. UNIX 기반 OS에서 소켓이라 함은 Special File로, 파일의 형태로 추상화된 통신 대상이다. 이 소켓을 대상으로 read/write를 수행하는 것으로 대상의 통신을 받거나 보낼 수 있다.

즉 통신 대상을 나타내는 객체가 된다.

일반적인 파일을 `open` 으로 생성했다면, 소켓은 `socket` 으로 생성함.
# Signature


``` C
#include <sys/types.h>
#include <sys/socket.h>

int socket(int domain, int type, int protocol)
```

## Parameters

### 1. domain

통신 도메인을 지정하는 인자. 통신할 프로토콜 패밀리를 지정한다. 보통 프로토콜 기반 체계를 의미하는 PF(Protocol Family)계열과 주소 기반 체계를 의미하는 AF(Address Family)계열이 존재한다.

현재 IPV4를 주로 사용하기 때문에 PF_INET 혹은 AF_INET을 주로 사용함.
### 2. type

소켓의 통신 방식을 지정하는 인자. 필요로 하는 통신 방식에 따라서 여러 설정을 할 수 있음.

쉽게 말하면 TCP인지 UDP인지 결정 가능함.

- SOCK_STREAM : TCP, 순서 보장, 신뢰 보장, 연결 지향, 안전하지만 느림
- SOCK_DGRAM : UDP, 순서 및 안정성 보장 없음, 연결 없음, 빠르지만 안전하지 않음
### 3. protocol

해당 소켓에 함께 사용할 특정 프로토콜을 지정하는 인자. 앞의 두 인자를 지원하는 프로토콜이 단 하나라면 0을 전달하면 되지만, 그 외의 경우 별도로 고를 수 있음. 그 외에 명시적으로 지정할 수 있음. 보통 다음의 둘 중 하나를 선택함.

- IPPROTO_TCP
- IPPROTO_UDP
## Return values

정상적으로 실행되었다면, Socket 파일의 FD가 반환됨. 다만, 소켓의 생성 과정에서 에러가 발생한다면 -1을 반환함.
