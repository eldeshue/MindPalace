---
Date: 2024-11-23
---
# Overview
OS가 제공하는 여러 이벤트에 대한 일반화된 처리를 가능하게 하는 커널 오브젝트, kqueue는 kernel event queue를 의미한다.  BSD 계열 OS(mac os 포함)에서 구현됨.

단순 fd에 대한 알림 뿐 아니라, 여러 시그널, 자식 프로세스, 비동기 IO까지 지원하는 다재다능한 시스템콜.

[select](select.md)의 개선된 구현이며, 리눅스의 epoll, 윈도우의 IOCP에 대응되는 위치지만, BSD 계열에만 존재하기에 이식성이 떨어진다는 단점이 있음.

>https://man.freebsd.org/cgi/man.cgi?query=EV_SET&sektion=3&apropos=0&manpath=FreeBSD%2B10.0-RELEASE
# Descriptions


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

EV_SET(kev, ident, filter, flags, fflags, data, udata);
```

## Parameters

### kqueue
커널 이벤트 queue(이하 kqueue) 오브젝트를 생성한다. kqueu 오브젝트는 운영체제가 제공하는 여러 이벤트에 대한 일반화된 알림을 수행한다.

```C
int kqueue(void);
```
성공적으로 호출 시 kqueue 오브젝트에 대한 fd를 제공한다. 그 외의 경우 -1을 반환한다.

### kevent

```C
int kevent(int kq, const struct kevent *changelist, int nchanges, struct kevent *eventlist, int nevents, const struct timespec *timeout);
```
kqueue가 이벤트를 처리하는 구조를 생성한다면, 실제 이벤트 관련 처리는 kevent 함수에서 수행된다. kevent는 인자로 전달된 여러 이벤트의 옵션에 따라서 다양한 동작을 한다.

이렇듯 kevent는 상당히 복잡한 구조를 가지고 있으며, 이를 잘 활용하기 위해서는 여러 예시의 참고가 필요하다.
#### parameters

1. kq : 앞서 kqueue()로 생성한 커널 큐의 fd.
2. changelist : kevent가 처리할 kevent 배열의 주소. kqueue에 대한 입력에 해당됨.
3. nchanges : changelist의 크기.
4. eventlist : kevent가 처리한 결과를 받아낼 배열의 주소. kqueue에 의한 출력에 해당됨.
5. nevents : eventlist의 크기
6. timeout : select와 마찬가지로 이벤트 처리를 대기할 시간을 설정.
#### return values

정상적으로 실행된 경우, eventlist에 반환된 이벤트의 개수를 return함. timeout을 초과한 경우에는 0, 모종의 사유로 에러가 발생한 경우 -1을 반환함.

> kevent 함수의 동작이 대단히 복잡한 관계로, 사용 예시를 확인하는게 좋아보임.

### kevent 관련 매크로

```C
struct kevent
{
	uintptr_t ident; // 이벤트의 식별자, fd나 포인터 등...
	int16_t filter;  // 필터, 이벤트의 종류(read, wirte, signal, ...)
	uint16_t flags;  // 이벤트의 동작 플래그(kqueue에 등록 및 삭제, ...)
	uint32_t fflags; // 필터의 플래그, 동작마다 다름
	intptr_t data;   // 플래그 별 데이터 반환값, 읽은 바이트 수 등...
	void *udata;     // 이벤트 별 데이터
};

EV_SET(kev, ident, filter, flags, fflags, data, udata);
```
kqueue가 제공하는 여러 이벤트는 kevent 구조체로 표현되는데, 이를 다루기 위한 여러 보조 매크로 함수가 존재한다. 첫 인자로 초기화할 kevent 구조체의 주소를 전달한다.

EV_SET의 경우, kevent를 초기화 하는 매크로 함수다. 

# Example

> from : https://man.freebsd.org/cgi/man.cgi?query=EV_SET&sektion=3&apropos=0&manpath=FreeBSD%2B10.0-RELEASE

```C
#include	<sys/event.h>
#include	<err.h>
#include	<fcntl.h>
#include	<stdio.h>
#include	<stdlib.h>
#include	<string.h>

int main(int	argc, char **argv)
{
	struct kevent event;	   /* Event we want to monitor */
	struct kevent tevent;   /* Event triggered */
	int kq, fd, ret;
	
	if (argc != 2)
	   err(EXIT_FAILURE, "Usage: %s path\n", argv[0]);
	fd =	open(argv[1], O_RDONLY);
	if (fd == -1)
	   err(EXIT_FAILURE, "Failed to open '%s'",	argv[1]);
	
	/* Create kqueue. */
	kq =	kqueue();
	if (kq == -1)
	   err(EXIT_FAILURE, "kqueue() failed");
	
	/* Initialize kevent	structure. */
	EV_SET(&event, fd, EVFILT_VNODE, EV_ADD | EV_CLEAR, NOTE_WRITE,
	   0, NULL);
	/* Attach event to the kqueue. */
	ret = kevent(kq, &event, 1, NULL, 0,	NULL);
	if (ret == -1)
	   err(EXIT_FAILURE, "kevent register");
	
	for (;;) {
	   /* Sleep	until something	happens. */
	   ret = kevent(kq,	NULL, 0, &tevent, 1, NULL);
	   if (ret == -1) {
	   err(EXIT_FAILURE, "kevent wait");
	   } else if (ret >	0) {
	   if (tevent.flags & EV_ERROR)
		   errx(EXIT_FAILURE, "Event error:	%s", strerror(event.data));
	   else
		   printf("Something was written in	'%s'\n", argv[1]);
	   }
	}
	
	/* kqueues are destroyed upon close() */
	(void)close(kq);
	(void)close(fd);
}
```

# Reference

-  https://man.freebsd.org/cgi/man.cgi?query=EV_SET&sektion=3&apropos=0&manpath=FreeBSD%2B10.0-RELEASE
- https://hyeonski.tistory.com/9
