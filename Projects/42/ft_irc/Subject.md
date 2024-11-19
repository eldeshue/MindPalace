---
Date: 2024-11-19
---
# Overview - IRC

## Internet Relay Chat

인터넷 릴레이 채팅, 즉 인터넷을 경유하여 채팅(텍스트) 기능을 제공하는 통신 프로토콜. 릴레이(중계)인 이유는 서버와 클라이언트 뿐 아니라, 서버와 서버도 연결되어 데이터를 중계하기 때문임.

해당 과제에서는 상용 IRC 클라이언트와 통신이 가능한 **IRC 서버**를 구현해야 함.

# Requirements

구현 세부사항은 다음과 같음.

## 안정성

서버 프로그램인 만큼, 높은 수준의 안정성을 필요로 함. 
- 예측 불가능한 종료가 없어야 함. 심지어 메모리가 부족한 경우에도 마찬가지
	-> exception을 적극적으로 사용할 필요가 있어보임.

## 개발 환경
- C++98 표준 준수
- C언어의 함수 사용 가능
- 외부 라이브러리 사용 불가능.
### MacOs 관련
write() 사용 관련해서 non-blocking한 사용을 위해서 FD에 fcntl()을 사용할 필요가 있음.
``` MacOs Only
fcntl(fd, F_SETFL, O_NONBLOCK);
```

## 통신 방식

- **Server-to-Client only**, must not implement server-to-server communicatioin.
- 동시에 여러 클라이언트와 소통 가능해야 함.
- Forking 금지
- Non-blocking 
- 단일 polling
- 여러 Client 중 하나를 선정
- TCP/IP
## 기능 세부

- 사용자 인증
- 별명 및 이름 설정
- 채널 합류
- 단일 대상을 향하는 Private message 기능
- 메시지 전달 : 채널을 대상으로 하는 메시지는 모두에게 보여야 함(마치 카톡)
- 사용자 권한 : Operator, User
### 채널 관리 명령어

- KICK : 사용자 추방
- INVITE : 사용자 초대
- TOPIC : 채널 주제 설정
- MODE : 채널 모드 설정 - 옵션에 따라 별도의 기능
	- i : 초대 모드 설정
	- t : TOPIC 커맨드 제한 설정 
	- k : 채널 비밀번호 설정
	- o : 채널 관리자 권한 설정(부여)
	- l : 채널에 대한 사용자 제한 설정


### Input

- Port number : 인터넷 연결을 위해서 필요한 포트 번호.
- Password : 연결을 위한 비밀번호, IRC 클라이언트와 소통을 위해서 필요함.

### Bonus Part
두 가지 기능을 추가해야 함
- File 전송
- bot 기능
# Allowed Systemcall

- socket, 
- close, 
- setsockopt, 
- getsockname, 
- getprotobyname, 
- gethostbyname,
- getaddrinfo, 
- freeaddrinfo, 
- bind, 
- connect, 
- listen, 
- accept, 
- htons, 
- htonl, 
- ntohs, 
- ntohl, 
- inet_addr, 
- inet_ntoa,
- send, 
- recv, 
- signal,
- sigaction, 
- lseek, 
- fstat, 
- fcntl, 
- poll (select, kqueue, epoll)