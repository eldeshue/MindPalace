---
Date: 2024-12-07
---

# Description
유닉스 소켓 프로그래밍을 위해서 추상화 한 TCP 소켓 객체.
호출하는 생성자의 종류(인자)에 따라서 소켓의 bind 방식이 달라집니다.

# lifetime & ownership
해당 소켓 객체는 그 bind된 대상의 종류에 따라서, 소유권이 달라집니다.

## Server(Listening) socket
main 루틴의 stack에 생성되어 프로세스가 종료될 때 까지 존속할 예정입니다.

서버 소켓의 경우 별도의 소유권자는 없습니다.

## Client socket
client 객체가 소유하며, client 객체와 life-time을 함께합니다.

client 객체는 unordered_map으로 manual하게 관리될 예정입니다.

# Attributes
## Member Variable

### 1. kInetSocketAddrLen - static, const, private
소켓 객체에 예비된 InetSocketAddress타입 구조체의 크기.

컴파일 타임에 결정되는 값이므로, static const(constexpr)로 정의.

### 2. cur_inet_sock_addr_len_ - private
소켓 객체에 write될 sockaddr타입 구조체의 크기. 앞서 살펴본 kInetSocketAddrLen와의 비교를 통해서
accept된 sockaddr의 문제를 확인할 수 있음. 예비한 공간보다 큰 sockaddr을 가질 경우 sockaddr은 truncated되며, 문제 발생.

-> depricated 되었다는 근거 미상의 정보가 있음.

### 3. socket_fd_ - const, private
소켓의 file descriptor. systemcall에 전달되는 핸들. RAII.

생성 시점에 확정되기에 const

### 4. is_listen_only - const, private
소켓의 listening 여부를 위한 필드.
서버 소켓으로 생성되는 경우는 true,
클라이언트 소켓으로 생성되는 경우에는 false로 초기화됨.

생성 시점에 확정되기에 const

### 5. sockaddr_in
해당 소켓의 바인드 정보를 저장하는 구조체.
프로토콜 정보, 포트 번호, IP 주소를 저장함.

## constructor & destructor
디폴트 생성, 복사, 복사 할당 불가능함.

### 1. construct with std::string - Server socket
인자로 포트 번호를 std::string타입으로 받아서 서버 소켓을 생성함. IP는 INADDR_ANY로 초기화 하여 해당 system에 존재하는 모든 IP에 대해서 대응할 수 있도록 설정함.

- is_listen_only_를 true
- reuse address 설정됨.
- bind 호출함.
- listen 설정됨.

### 2. construct with fd - Client socket
인자로 listenting socket의 fd를 int로 받아서 accept를 호출, client 소켓을 생성함.

- is_listen_only_를 false
- reuse address 설정됨.

## Member Function

### 1. int socket_fd();
소켓의 fd에 대한 getter

### 2. bool is_listen_only();
getter, 소켓의 리스닝 설정 여부

### 3. in_port_t GetPortNum();
bind 및 accept된 소켓의 포트 번호, host byte order로 반환됨.

### 4. in_addr_t GetIPAddress();
bind 및 accept된 소켓의 IP 주소, host byte order로  반환됨.
