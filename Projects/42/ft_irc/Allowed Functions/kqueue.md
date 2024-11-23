---
Date: 2024-11-23
---
# Overview
동기화된 다중 IO를 지원하는 시스템 콜. BSD 계열 OS에서 사용됨.

[select](select.md)의 개선된 구현이며, epoll에 대응되는 위치.
# Signature


```C
#include <sys/time.h>
#include <sys/event.h>
#include <sys/types.h>

struct kevent
{
	uintptr_t ident; /* identifier for this event */
	int16_t filter; /* filter for event */
	uint16_t flags; /* action flags for kqueue */
	uint32_t fflags; /* filter flag value */
	intptr_t data; /* filter data value */
	void *udata; /* opaque user data identifier */
};

int kqueue(void);

int kevent(int kq, const struct kevent *changelist, int nchanges, struct kevent *eventlist, int nevents, const struct timespec *timeout);

```


## Parameters



## Return values


