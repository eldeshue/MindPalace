---
Date: 2024-11-24
---
# Overview
![[architecture_of_ft_irc.png]]
# Modules

## Channel
메시지를 주고 받는 clients의 그룹.

채널 내부에서는 메시지가 공유됨. 마치 단톡방.

채널을 첫 클라이언트가 참여하면서 생성되고, 마지막 클라이언트가 사라지면서 제거됨.

채널 소유권을 구현한 경우, operator의 결정에 따라서 빈 채널을 유지할 수 있음.

채널에는 여러 type이 존재하며, 이는 이름의 접두어로 구분함. 정책이 달라짐.

추후의 확장 가능성을 위해서는 채널 별로 IO를 분리 시켜야 함. 

- 채널의 이름, string
- 채널의 타입(추상 클래스, 생성시 정해짐) 및 모드(상태값, 빈번하게 변경 가능)
- 채널의 토픽, string
- 동시에 가입 가능한 채널 수 제한
- 클라이언트 객체에 대한 참조 -> **한 유저가 복수의 채널에 소속되는가?**
- 특정 클라이언트의 소속 여부를 빠르게 확인해줘야 함.
	-> disjoint set?
### Client
클라이언트와 연결된 소켓과 클라이언트의 권한 등을 관리하는 객체.

모든 클라이언트는 유니크한 nickname을 가지고, 이를 통해 식별됨. 이 nickname의 uniqueness 기준은 아마도 채널. 

서버는 연결된 클라이언트의 이름, 클라이언트가 돌아가는 호스트의 이름 및 주소(DNS or IP), 클라이언트가 연결된 서버 등의 정보를 알고 있어야 함. 

특별한 권한을 지닌 클라이언트인 operator가 존재함. 이들은 네트워크에 대한 권한이 존재. 특정 작업을 요청할 수 있음.

채널과 별도로 관리되어야 함.

- level of moderation : 채널에 대한 권한 레벨
- unique nickname
- host name/address
- client name
- name of server that nonnected to
- 소켓 object
## Server
kqueue 이벤트 루프를 통해서 입력을 식별(감지)하여 디스패처에게 전달한다.

멀티 스레드 서버의 경우, channel per thread, server per thread로 확장.

각 채널 자원들에 대한 참조를 획득하는 구조. -> 소유권 없음.

>수정 리뷰 : 서버가 기능을 수행하기 위해서 필요한 동작을 찾는게 디스패처. 현재의 구현은 서버 그 자체임. 책임을 분리해줘야 함. -> **보다 더 생각**
>문자열을 받는다 -> 문자열을 파싱한다 -> 파싱에 따라서 함수를 선택한다.

- 감시 대상 관련 자원 (kqfd, kevent vector OR fd_set, etc)을 참조(소유하지 않음)
- message에 대한 sanity check

## Dispatcher
서버로부터 받아낸 커맨드를 실제 동작으로 연결해주는 객체. 

- 커맨드 - 핸들러 매핑한 해시맵

싱글톤.
### Server Event
서버 소켓에서 비롯된 이벤트(사용자 추가 등). 별도 처리 필요.
### Client Event - Command
클라이언트 소켓에서 비롯된 이벤트.
### Parser
커맨드를 해석하는 모듈. Mapper의 key값을 생성함.

>메시지 분석 필요

- static member function
### Command Mapper
parser에서 해석한 커맨드를 key로, 핸들러를 결정하는 모듈. 핸들러는 callable.

key로 파싱 결과 토큰(string)을 받고, value로 핸들러(callable)을 반환하는 hash map
### Command Handlers
각각의 커맨드에 대응하는 처리를 담당하는 모듈. 
- KICK
- INVITE
- TOPIC
- MODE

함수 포인터 vs 함수 객체?