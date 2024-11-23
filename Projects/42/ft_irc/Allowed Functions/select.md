---
Date: 2024-11-23
---
# Overview
동기화된 다중 입출력을 제공하는 함수.

단일 스레드로 여러 대상과 동시에 IO하는 IO-Multiplexing을 위해 준비된 시스템 콜. fd_set이라는 자료구조에 등록한 fd들 중 IO가능한 상태인 fd의 등장을 기다린다(wait). 기본적으로  blocking 함수이나, timeout 인자를 통해서 제어 가능함.

이는 단일 스레드로 IO를 시도하는 경우, 프로세스 전체가 blocking되는 문제를 해결하기 위한 것으로, IO 대상의 준비 완료 여부를 확인하기 위함임.

다만, 한 번에 감시할 수 있는 fd의 개수가 1024개로 매우 적음. 따라서 이러한 문제를 해결한 개선된 API가 존재함. LINUX 계열에서는 poll 및 epoll, Mac Os에서는 kqueue를 활용함.

>**WARNING**: **select**() can monitor only file descriptors numbers that
       are less than **FD_SETSIZE** (1024)—an unreasonably low limit for
       many modern applications—and this limitation will not change.
       All modern applications should instead use [poll(2)](https://man7.org/linux/man-pages/man2/poll.2.html) or [epoll(7)](https://man7.org/linux/man-pages/man7/epoll.7.html),
       which do not suffer this limitation.
# Signature

```C
#include <sys/select.h>

typedef /* ... */ fd_set; // 감시할 fd를 등록할 자료구조

int select(int nfds,
		fd_set *_Nullable restrict readfds,
		fd_set *_Nullable restrict writefds,
		fd_set *_Nullable restrict exceptfds,
		struct timeval *_Nullable restrict timeout);

void FD_CLR(int fd, fd_set *set);   // set에서 fd를 제거함.
int  FD_ISSET(int fd, fd_set *set); // set에 fd가 등록되었는지 확인함.
void FD_SET(int fd, fd_set *set);   // set에 fd를 등록함
void FD_ZERO(fd_set *set);          // set 전체를 비움
```

## Parameters



## Return values

성공적으로 호출된 경우, IO가 가능한 fd의 개수를 반환한다. fd_set에는 바로 호출 가능한 fd만 남고 나머지는 제거된다.
