---
Date: 2024-11-22
---
# Overview
상대방의 connect 시도를 받아서 연결을 성립하고, 대상을 가리키는 소켓을 생성하여 그 fd를 반환하는 함수. 즉 TCP 등의 연결을 생성한다.

## Blocking / Non-blocking
별도의 옵션이 없다면(SOCK_NONBLOCK) 해당 함수는 blocking 함수로, connection 시도가 없으면 return 하지 않는다.

따라서 accept는 효율적으로 수행되어야 하며, 이에는 두 가지 방법이 존재한다. 하나는 select 계열 함수를 활용하여 listening 소켓의 ready 여부를 확인하고 accept를 호출하는 것이다. 이 경우 FD_SET에 listening 소켓을 등록해줄 필요가 있다.

다른 하나는 signal을 활용하는 것으로 SIGIO에 해당 소켓을 지정하고, 그 핸들러에서 accept를 호출하는 것이다.

>	In order to be notified of incoming connections on a socket, you
       can use [select(2)](https://man7.org/linux/man-pages/man2/select.2.html), [poll(2)](https://man7.org/linux/man-pages/man2/poll.2.html), or [epoll(7)](https://man7.org/linux/man-pages/man7/epoll.7.html).  A readable event will
       be delivered when a new connection is attempted and you may then
       call **accept**() to get a socket for that connection.
       Alternatively, you can set the socket to deliver **SIGIO** when
       activity occurs on a socket; see [socket(7)](https://man7.org/linux/man-pages/man7/socket.7.html) for details.

       https://man7.org/linux/man-pages/man2/accept.2.html

# Signature

```C
#include <sys/socket.h>

int accept(int sockfd , struct sockaddr *_Nullable restrict  addr, socklen_t *_Nullable restrict addrlen);
```

## Parameters

### 1. sockfd
SOCK_STREAM, SEQ_PACKET으로 socket, bind, listen 모두 거친 소켓의 fd.

생성시 SOCK_NONBLOCK을 설정하지 않았다면 blocking 함수임. 다만, select와 함께 사용하는 경우 SOCK_NONBLOCK은 의미 없음(ready된 소켓의 fd에만 accept를 호출하므로).

### 2. addr
sockaddr 구조체의 포인터, sockaddr_in 등이 이에 해당됨. NULL을 전달하면 채워주지 않음. 이 경우 sockaddr의 정보가 필요하면 getsockname 함수 호출 필요함.

### 3. addrlen
sockaddr의 크기. 본래의 크기보다 그 값이 작으면 truncate 됨.
## Return values
성공적으로 호출된 경우, 연결된 대상을 향하는 socket의 fd를 반환함. 따라서 반환된 fd는 bind 불필요함.

실패한 경우 -1. 다만 SOCK_NONBLOCK으로 설정하고 연결 대상이 존재하지 않아서 즉시 return하는 경우, errno값을 EAGAIN 혹은 EWOULDBLOCK으로 세팅함. select 계열과 함께 사용하면 볼 일 없음.
