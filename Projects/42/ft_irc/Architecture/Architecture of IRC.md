---
Date: 2024-11-24
---
# Overview
![[architecture_of_ft_irc.png]]
# Modules

## Server
클라이언트 사이의 통신을 중개하며, 채널을 관리하는 주체.

이벤트 루프(kqueue or select)를 돌려서 여러 이벤트를 처리함.
채널의 추가 및 삭제, 채널에 대한 클라이언트의 추가 및 삭제. 

- 채널 객체를 관리할 컬렉션(vector?, list?)
- 이벤트 루프

싱글톤 패턴?
### Channel
메시지를 주고 받는 clients의 그룹. 서버는 채널을 통해서 클라이언트를 관리함.

채널 내부에서는 메시지가 공유됨. 마치 단톡방.

채널을 첫 클라이언트가 참여하면서 생성되고, 마지막 클라이언트가 사라지면서 제거됨. 채널 소유권을 구현한 경우, 빈 채널을 유지할 수 있음.

채널에는 여러 type이 존재하며, 이는 이름의 접두어로 구분함. 정책이 달라짐.

추후의 확장 가능성을 위해서는 채널 별로 IO를 분리 시켜야 함. 

- 채널의 이름
- 클라이언트 객체를 관리할 컬렉션(unordered_map)
- 특정 클라이언트의 소속 여부를 빠르게 확인해줘야 함.
	-> disjoint set?
#### Client
클라이언트와 연결된 소켓과 클라이언트의 권한 등을 관리하는 객체. 채널에 소유권이 존재함.

모든 클라이언트는 유니크한 nickname을 가지고, 이를 통해 식별됨. 이 nickname의 uniqueness 기준은 아마도 채널. 

서버는 연결된 클라이언트의 이름, 클라이언트가 돌아가는 호스트의 이름 및 주소(DNS or IP), 클라이언트가 연결된 서버 등의 정보를 알고 있어야 함. 

특별한 권한을 지닌 클라이언트인 operator가 존재함. 이들은 네트워크에 대한 권한이 존재. 특정 작업을 요청할 수 있음.

- unique nickname
- host name/address
- client name
- 
## Dispatcher
입력을 식별하고, 입력으로 들어온 커맨드를 해석하고, 그에 따른 동작을 결정하는 모듈. 
### Server Event
서버 소켓을 대상으로 하는 이벤트는 별도 처리 필요.
### Client Event - Command

### Parser
커맨드를 해석하는 모듈. Mapper의 key값을 생성함.
### Command Mapper
parser에서 해석한 커맨드를 key로, 핸들러를 결정하는 모듈. 핸들러는 callable.
### Command Handlers
각각의 커맨드에 대응하는 처리를 담당하는 모듈. 
- KICK
- INVITE
- TOPIC
- MODE

함수 포인터 vs 함수 객체?