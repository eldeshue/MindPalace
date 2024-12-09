---
Date: 2024-12-09
---
# Description
IRC 클라이언트와 관련된 데이터를 관리하는 객체. 

client라 하면, 서버와 연결된 노드 중에서 서버를 제외한 모든 대상을 뜻한다. 네트워크에서 말단(leaf)를 이루며, 실질적인 통신의 주체다. 서버는 이들 클라이언트의 요청을 수행하고, 클라이언트의 메시지를 대신 전달하는 매개자에 불과하다.

# Lifetime & Ownership
case-mapped name(string)을 key로 하는 해시맵, clients에서 관리됨.

connection이 해제될 때(quit 혹은 비정상 종료) 명시적으로 제거됨.

# Inheritance
- NON

# Traits
- unique(non-copyable)
- no-default
# Attribute

## Member Variables

### 1. nick_name_ - private, const 
채널 내에서 사용자를 식별하기 위한 이름(string). 채널 내에서 고유해야 함.

registration 과정에서 NICK command로 획득. 

nick name에는 conventioin이 존재하므로, 초기화 하기 전에 check 필요함.
### 2. user_name_ - private, const
nick name이 채널에서만 유용한, 단순 식별을 위한 이름이라면, user_name은 보다 실명에 기반하는 이름이다. 

user name을 획득하는 방법에는 두 가지가 있는데, 하나는 ident protocol로 획득하는 것이고, 다른 하나는 USER 커맨드로 client에서 획득하는 것이다. ident protocol로 획득하는 경우, 이는 잘 인증된 이름이다. 다만, 클라이언트가 실행되는 host 쪽에서 ident server가 준비되어 있어야 인증이 가능 하므로, 획득하지 못할 수 있다. 

> ident 서버로 인증된 user name이 우선이며, 단순 USER 커맨드로 획득한 경우, 앞에 '~'를 붙여서 구분한다. 

### 3. real_name_ - private, const
user name과 마찬가지로, registration 과정 중 USER 커맨드로 획득하는 이름이다.

### 4. socket object - private, pointer, nullable
네트워크 정보를 포함하는 TcpSocket 객체의 포인터.

**포인터로 설정한 이유는 소켓의 비정상 종료로 인한 자원 제거를 처리하기 위함임.**

### 5. Permission - private
사용자가 소속된 채널에 대해 갖는 권한(오퍼레이터 등)에 대한 값. 열거형으로 처리될 예정임.  

설정에 따라서 바뀔 수 있는 값이므로, mutable.

### 6. Server connected from(Not Necessary)
IRC 네트워크에서 클라이언트는 해당 서버와 직접 연결되지 않을 수 있다. 이 경우 해당 클라이언트는 경유하는 서버를 갖는데, 이 경유하는 서버의 정보를 알고 있어야 한다. 

> 이는 과제의 요구 사항이 아니므로, 생략 가능하다.

## Member Functions

### 1. Constructor
프로세스 내에서 유일하게 관리되어야 하는 자원이므로, **복사 불가능**하다. 또한 connection이 없으면 클라이언트는 생성되지 않으므로, default 생성도 불가능하다.

- construct with listening fd : client를 향하는 소켓을 accept하기 위한 server bind된 소켓의 fd
### 2. Destructor
TcpSocket 객체에 대한 pointer를 delete 해줘야 함. 비정상 종료로 인해서 client와 별개로 delete 될 수 있다고 생각함. 이 경우, client도 제거해줘야 함.

### 3. getter
각 원소(이름, 소켓 등)에 대한 gettter.

### 4. ClearSocket
socket의 비정상 해제 시 호출, 포인터에 대해서 delete한 후 NULL로 값을 바꾼다.
### 5. ReceiveMessage - public

TcpSockt의 fd에서 내부적인 buffer로 read, std::string으로 return한다. return value는 dispatcher로 전달됨. 

이 때, read buffer의 size는 최대 4096 + 512 Byte이다.

>**Most IRC servers limit messages to 512 bytes in length,** including the trailing `CR-LF` characters. Implementations which include [message tags](https://ircv3.net/specs/extensions/message-tags.html) need to allow additional bytes for the **tags** section of a message; clients must allow 8191 additional bytes and **servers must allow 4096 additional bytes**.
### 6. SendMessage - public

적절한 응답을 위해서 formatting된 string을 인자로 받아서 c_str()으로 소켓에 write한다.