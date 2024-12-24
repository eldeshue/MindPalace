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

### 1. nick_name_ - private, mutable 
채널 내에서 사용자를 식별하기 위한 이름(string). 채널 내에서 고유해야 함.

registration 과정에서 NICK command로 획득.  NICK 커맨드에 의해서 변경 가능함.
필요에 따라서 NICK으로 언제든지 변경 가능함. 

nick name에는 conventioin이 존재하므로, 초기화 하기 전에 check 필요함.
### 2. user_name_ - private, const
nick name이 채널에서만 유용한, 단순 식별을 위한 이름이라면, user_name은 서버 내부적으로 관리하는 클라이언트 관리를 위한 이름임.  nickname은 채널 내 식별을 위한 이름이라 고유하지만 쉽게 바꿀 수 있으므로 서버는 user name으로 사용자를 구분해야 함.

user name을 획득하는 방법에는 두 가지가 있는데, 하나는 ident protocol로 획득하는 것이고, 다른 하나는 USER 커맨드로 client에서 획득하는 것이다. ident protocol로 획득하는 경우, 이는 잘 인증된 이름이다. 다만, 클라이언트가 실행되는 host 쪽에서 ident server가 준비되어 있어야 인증이 가능 하므로, 획득하지 못할 수 있다. 

> ident 서버로 인증된 user name이 우선이며, 단순 USER 커맨드로 획득한 경우, 앞에 '~'를 붙여서 구분한다. 

한 번 연결을 확립하면 변경할 수 없음. 
### 3. real_name_ - private, const
user name과 마찬가지로, registration 과정 중 USER 커맨드로 획득하는 이름이다.

**채널 내 사용자 사이에서 정보를 공유하기 위한 목적**으로 유지하는 데이터이다.
### 4. socket_ - private, nullable
네트워크 정보를 포함하는 TcpSocket 객체.

**host name에 해당하는 필드임**

### 5. permission_ - private
사용자가 소속된 채널에 대해 갖는 권한(오퍼레이터 등)에 대한 값. 열거형으로 처리될 예정임.  

설정에 따라서 바뀔 수 있는 값이므로, mutable.

### 6. Server connected from(Not Necessary)
IRC 네트워크에서 클라이언트는 해당 서버와 직접 연결되지 않을 수 있다. 이 경우 해당 클라이언트는 경유하는 서버를 갖는데, 이 경유하는 서버의 정보를 알고 있어야 한다. 

> 이는 과제의 요구 사항이 아니므로, 생략 가능하다.

### 7. left_buffer_ - private, mutable
소켓에서 읽어온 데이터를 임시로 저장하는 string 변수. 정식 message의 형태가 될 때 마다 비워짐.

non-block 소켓이므로, read의 양에 대한 고려가 필요함. 여러 메시지에 대한 내용이 한 번에 들어올 수 있으므로, per socket 한 read buffer를 두어 순차적으로 처리할 필요가 있다고 생각됨. 

하나의 message로 포매팅 될 때 마다 Dispatcher로 전달될 예정임.
## Member Functions

### 1. Constructor
프로세스 내에서 유일하게 관리되어야 하는 자원이므로, **복사 불가능**하다. 또한 connection이 없으면 클라이언트는 생성되지 않으므로, default 생성도 불가능하다.

- construct with listening fd : client를 향하는 소켓을 accept하기 위한 server bind된 소켓의 fd
### 2. Destructor

### 3. getter
각 원소(이름, 소켓 등)에 대한 gettter.

### 4. ReceiveMessage - public

TcpSockt의 fd에서 system buffer로 read, optional<std::string>으로 return한다. return value는 dispatcher로 전달됨. 

이 때, read buffer의 size는 최대 4096 + 512 Byte이다.

>**Most IRC servers limit messages to 512 bytes in length,** including the trailing `CR-LF` characters. Implementations which include [message tags](https://ircv3.net/specs/extensions/message-tags.html) need to allow additional bytes for the **tags** section of a message; clients must allow 8191 additional bytes and **servers must allow 4096 additional bytes**.

함수의 local한 char read_buffer[]를 전달하여 읽은 후, 온전한 message는 return하고 남은 내용은 left_buffer_에 저장함.
### 5. SendMessage - public

적절한 응답을 위해서 formatting된 string을 인자로 받아서 c_str()으로 소켓에 write한다.