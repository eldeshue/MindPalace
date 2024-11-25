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

싱글톤 패턴?
## Channel
메시지를 주고 받는 clients의 그룹. 서버는 채널을 통해서 클라이언트를 관리함.

- 특정 클라이언트의 소속 여부를 빠르게 확인해줘야 함.
### Client
## Dispatcher
입력으로 들어온 커맨드를 해석하고, 그에 따른 동작을 결정하는 모듈. 
### Parser
커맨드를 해석하는 모듈.
### Command Mapper
파서에서 해석한 커맨드를 바탕으로, 커맨드 핸들러를 결정하는 모듈.
### Command Handlers
각각의 커맨드에 대응하는 처리를 담당하는 모듈. callable.
