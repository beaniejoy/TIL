# HTTP

- HTTP통신
  - Client가 요청을 보내는 경우에만 Server가 응답하는 단방향 통신
  - Server로부터 응답을 받은 후에는 연결이 바로 종료
  - 실시간 연결이 아니고, 필요한 경우에만 Server로 요청을 보내는 상황에 유리
  - 요청을 보내 Server의 응답을 기다리는 어플리케이션의 개발에 주로 사용
  - `TCP/IP`를 이용하는 application protocol
  - 비연결성 프로토콜(`stateless`)이기 때문에 cookie, session 개념이 등장
- Socket 통신
  - Server와 Client가 계속 연결을 유지하는 양방향 통신
  - Server와 Client가 실시간으로 데이터를 주고받는 상황이 필요한 경우에 사용
  - 실시간 동영상 Streaming이나 온라인 게임 등과 같은 경우에 자주 사용

## TCP, UDP

IP 프로토콜 위에서 작동하는 `Transport Layer`
  
### TCP (Transmission Control Protocol)
- IP 프로토콜 위에서 연결형 서비스를 지원하는 전송 계층 프로토콜
인터넷 환경에서 기본으로 사용
- 연결형 서비스, 가상 회선 방식
발신지, 수신지를 연결해 패킷을 전송하기 위한 논리적 경로를 설정
- 높은 신뢰성
`3-way, 4-way handshaking`을 통해 정확한 전송을 보장
- UDP보다 속도가 느리다.
- 전이중 방식(Full Duplex)의 양방향 가상 회선을 제공

### UDP (User Datagram Protocol)
- 비연결형 서비스 제공
- 헤더와 전송 데이터에 대한 체크섬 기능을 제공
- [Best Effort](https://en.wikipedia.org/wiki/Best-effort_delivery) 전달 방식을 지원
- 전송한 데이터그램이 목적지까지 잘 도착했는지 확인하지 않기 때문에 신뢰성은 떨어진다.
- 데이터 처리가 빠르기 때문에 데이터 전송시간에 민감한 환경에서 UDP 사용  
(스트리밍 서비스, 실시간 중계, 게임 등, DNS 서버도 UDP를 사용)s