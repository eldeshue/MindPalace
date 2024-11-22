---
Date: 2024-11-22
---
# Overview
생성, bind된 소켓을 read only하게 설정한다. 해당 소켓의 write 기능을 봉인한다. 

기존의 소켓은 read/write의 IO 모두를 지원한다. 그러나 **accept 함수가 blocking함수라** 부득이하게 연결을 대기하는 소켓과 실제 데이터를 처리하는 소켓을 분리할 필요가 생긴다.

accept의 대상이 되는 연결 지향적인 소켓(TCP 등)의 경우, 필수적인 설정이다.
# Signature


```C
#include <sys/socket.h>

int listen(int sockfd, int backlog);
```

## Parameters

### 1. sockfd
SOCK_STREAM, SOCK_SEQPACKET의 옵션으로 생성된 소켓의 fd. 

## 2. backlog
connection 대기열의 최대 크기. 지정한 값보다 많은 연결 요청이 들어오면, 에러를 발생시킴.

## Return values

성공하면 0, 실패하면 -1.