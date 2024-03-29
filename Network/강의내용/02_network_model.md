# Network Model

- TCP/IP 모델
  - 이게 먼저 시작(1960년대말)
  - 네트워크 인터페이스, 네트워크, 전송, 응용 - 4 계층으로 이루어짐
- OSI 7 Layer
  - 1984년 체계화
  - ISO에서 표준으로 지정한 모델
  - 물리, 데이터링크, 네트워크, 전송, 세션, 표현, 응용: 7 계층으로 이루어짐

<br>

## :pushpin: OSI 7 Layer 계층별 프로토콜

- 1계층 물리: 전선, 전파, 광섬유, 동축케이블, 도파관, PSTN, 리피터, DSU, CSU, 모뎀
- 2계층 데이터링크: **이더넷**, 토큰링, PPP, HDLC, 프레임 릴레이, ISDN, ATM, 무선랜, FDDI
- 3계층 네트워크: **IP**, **ICMP**, IGMP, X.25, CLNP, **ARP**, RARP, BGP, OSPF, RIP, IPX, DDP
- 4계층 전송: **TCP**, **UDP**, SPX
- 5계층 세션: NetBIOS
- 6계층 표현: SMB, AFP, XDR
- 7계층 응용: **HTTP**, SMTP, IMAP, POP, SNMP, **FTP**, TELNET, **SSH**

<br>

## :pushpin: 두 모델의 차이점
- 공통점:
  - 계층적 네트워크 모델
  - 계층간 역할 정의
- 차이점
  - 계층의 수 차이
  - OSI는 역할 기반, TCP/IP는 프로토콜 기반
  - OSI는 통신 전반에 대한 표준
  - TCP/IP는 데이터 전송기술 특화

<br>

## :pushpin: 패킷
- 네트워크 상에서 전달되는 데이터를 통칭하는 말
- 네트워크에서 전달하는 데이터 형식화된 블록
- 패킷은 제어 정보와 사용자 데이터로 이루어지며, 사용자 데이터는 `페이로드(payload)`라고 한다.

```text
헤어 | 페이로드 | 풋터(잘 활용 X)  
ex) Ethernet | IPv4 | TCP | HTTP
```
페이로드에 프로토콜 헤더를 붙이는 과정을 encapsulation(캡슐화)  
위 계층부터 차례대로 붙는 형태가 되어야 한다. (순차적, 혼합될 수 없음)  
간혹 `1 - 2 - 3 - 3 - 4` 이런 식으로 같은 계층의 내용 여러 번 헤더에 들어갈 수 있다.  

데이터를 확인할 때에는 맨 하위 계층부터 차례대로 하나씩 깐다.

<br>

## PDU
- Protocol Data Unit

계층별로 데이터 유닛을 부르는 이름이 다르다. (payload)
- 4계층: **Segment**(세그먼트)
- 3계층: **Packet**(패킷)
- 2계층: **Frame**(프레임)