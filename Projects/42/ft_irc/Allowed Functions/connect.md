---
Date: 2411-02-02
---
# Overview
대상을 향해 향해 연결을 시도하는 시스템 콜.  상대방이 accept를 수행하면 연결이 성립되고, 정상적으로 소통이 가능해짐.

사실상 서버의 bind에 해당하는 함수.
# Signature

```C
#include <sys/socket.h>

int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

## Parameters
### 1. sockfd
socket으로 생성한 소켓의 fd. 
### 2. addr
상대방을 향해 bind된 소켓의 정보를 저장할 sockaddr구조체의 포인터.
### 3. addrlen
2에서 전달한 포인터가 가리키는 sockaddr 구조체의 크기.

## Return values

정상적으로 연결을 확립하면 0. 실패하면 -1.
