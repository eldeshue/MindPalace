---
Date: 2024-11-22
---
# Overview
connect 시도를 받아서 대상을 가리키는 소켓의 fd를 반환하는 함수. 즉 TCP 등의 연결을 생성한다.

해당 함수는 blocking 함수로, connection 시도가 없으면 return 하지 않는다.

# Signature


```C
#include <sys/socket.h> 

int accept(int sockfd , struct sockaddr *_Nullable restrict  addr, socklen_t *_Nullable restrict addrlen);
```


## Parameters



## Return values


