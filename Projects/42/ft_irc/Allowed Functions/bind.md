---
Date: 2024-11-22
---
# Overview
생성한 소켓에 주소를 지정(bind) 한다. 소켓 생성 시점에 프로토콜이 결정되지만, 실제 소켓을 사용하기 위해서는 bind를 통해서 주소를 지정해줘야 한다.  

# Signature


```C
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

## Parameters

### 1. _sockfd_
주소를 지정하고자 하는 소켓의 FD
### 2. _addr_
해당 소켓에 지정하고자 하는 주소(IP, port, etc)의 정보를 저장하는 구조체의 포인터.

bind함수는 여러 프로토콜에 대응되도록, 일반화된 함수이다. 따라서 지정된 프로토콜의 종류에 따라 내용이 서로 다른 sockaddr을 참조하도록 설계되어있다. 

다음은 sockaddr 중 IPv4를 의미하는 INET 소켓, sockarrd_in의 구조이다.

```C
struct sockaddr_in {
	short    sin_family;          // 주소 체계: AF_INET
	u_short  sin_port;            // 16 비트 포트 번호, network byte order
	struct   in_addr  sin_addr;   // 32 비트 IP 주소, network byte order
	char     sin_zero[8];         // 전체 크기를 16 비트로 맞추기 위한 dummy
};

struct  in_addr {
	u_long  s_addr;     // 32비트 IP 주소를 저장 할 구조체, network byte order
};
```

### 3. addrlen
두 번째 인자로 전달되는 sockaddr의 포인터가 가리키는 구조체의 크기. memory safety를 위함.

sockaddr_in의 경우는 16바이트로 맞춰져 있음.

## Return values
성공하면 0, 실패하면 -1.


