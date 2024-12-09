---
Date: 2024-11-19
---
# Overview - IRC

## Internet Relay Chat

인터넷 릴레이 채팅, 즉 인터넷을 경유하여 채팅(텍스트) 기능을 제공하는 통신 프로토콜. 릴레이(중계)인 이유는 서버와 클라이언트 뿐 아니라, 서버와 서버도 연결되어 데이터를 중계하기 때문임.

해당 과제에서는 상용 IRC 클라이언트와 통신이 가능한 **IRC 서버**를 구현해야 함.

참고 : https://modern.ircdocs.horse/

# Requirements

구현 세부사항은 다음과 같음.

구현 내용 참고 : https://github.com/solanum-ircd/solanum

## 안정성

서버 프로그램인 만큼, 높은 수준의 안정성을 필요로 함.
- 예측 불가능한 종료가 없어야 함. 심지어 메모리가 부족한 경우에도 마찬가지
	-> exception을 적극적으로 사용할 필요가 있어보임.

## 개발 환경
- C++98 표준 준수
- C언어의 함수 사용 가능
- 외부 라이브러리 사용 불가능.
- Google C++ style
	-> 예외적으로 exception 허용한다.
- cpplint
- Google Test
### MacOs 관련
write() 사용 관련해서 non-blocking한 사용을 위해서 FD에 fcntl()을 사용할 필요가 있음. 오직 이 용도로만 fcntl을 사용해야 함.
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

## Socket 관련
소켓을 활용한 네트워크 프로그래밍에 핵심적인 역할을 하는 함수들
- [socket](socket.md) : 소켓 객체를 생성함
- [close](close.md) : 소켓 객체를 제거함
- [bind](bind.md) : 소켓에 주소를 지정
- [listen](listen.md) : 서버 측 소켓에 연결 대기를 설정함, listen only 설정
- [accept](accept.md ) : 통신 대상과 연결된 소켓을 생성, blocking 함수, select 등으로 non-blocking 하게 동작
- [connect](connect.md) : 소켓에 통신 대상을 연결함, 클라이언트가 호출함. bind를 포함함.
- [select](select.md) : FD들을 모니터링함, 이후 특정 FD가 가리키는 소켓에 데이터가 수신되면 이를 감지함
- [kqueue](kqueue.md) : select의 개선된 버젼
- send : 데이터 전송, write로 대체 가능, flag로 보다 세밀한 옵션 설정 가능함
- recv : 데이터 수신, read로 대체 가능, flag로 보다 세밀한 옵션 설정 가능함
## sockaddr 구조체 관련
소켓의 FD를 인자로 받아서 해당 소켓의 정보를 sockaddr 구조체에 채워서 돌려줌.
- setsockopt : 소켓에 옵션을 설정
- getsockname : 소켓의 세부 정보를 갖는 sockaddr 구조체를 초기화 함. bind 후 사용.

## DNS 관련
Domain name으로 정보(프로토콜, 호스트 주소)를 얻는 류의 함수
- getprotobyname : 도메인 이름으로 해당 프로토콜의 정보를 획득, 데이터베이스(?)를 순회하면서 탐색
- gethostbyname : 도메인 이름으로 해당 호스트 주소(IP)를 획득
- getaddrinfo :  도메인 이름으로 호스트 주소(IP)등을 저장한 addrinfo를 받아오는 함수
- freeaddrinfo : addrinfo 구조체를 free하는 함수

## 네트워크 데이터 변환 관련
네트워크 관련 데이터들의 변환을 위한 함수. endian 변환, IP 변환, 등등
- htons : host to network short, 호스트의 short 데이터의 바이트 오더를 네트워크의 오더로 변환
- htonl : host to network long, 호스트의 long 데이터의 바이트 오더를 네트워크의 오더로 변환
- ntohs : network to host short, short는 포트번호이고 long은 IP어드레스
- ntohl : network to host long, 보통 호스트는 little endian이고 네트워크는 big endian이므로 변환
- inet_addr : 문자열 형태의 IP주소를 정수 값으로 변환, unsigned long을 반환
- inet_ntoa : unsigned long의 IP주소를 문자열의 형태로 반환

## 시그널 관련
우리가 작성할 IRC 서버는 무한루프, 따라서 interrupt로 종료해야 함. 프로세스 종료 시 자원 반환을 위해서 핸들러 등록이 필요함.
- signal : signal의 발생에 따른 handler를 등록하는 함수. 시그널을 이용하여 비순차적 실행
- sigaction : signal함수와 유사하지만, 보다 세세한 설정이 가능함.

## 파일 관련
- read/write : recv, send 대신 socket IO 가능
- lseek : fd 내부를 가리키는 커서를 이동, read 및 write 위치를 조정함
- fstat : 전달한 fd의 status에 대한 구조체를 반환함
- fcntl : 파일과 관련된 설정을 변경하는 함수, 비동기 IO를 위해서만 사용 가능함.
