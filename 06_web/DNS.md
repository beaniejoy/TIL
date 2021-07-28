# DNS

- Domain Name Server
- 호스트의 도메인 이름을 호스트의 네트워크 주소(IP)로 바꾸거나 그 반대의 변환을 수행할 수 있도록 하기 위해 개발된 시스템
- `캐시 서버`: 클라이언트가 요청한 url을 받아서 도메인레벨에 따라 해당 ip주소값을 조회해주는 DNS서버
- `콘텐츠 서버`: 외부 호스트로부터 본인이 관리하는 도메인에 관한 조회를 받는 DNS 서버, 각 도메인 구분별로 관리하는 영역이 있다. (kr, com, naver, 이런 식으로 관리하는 영역이 존재)

## 도메인 이름 형성

- 도메인명은 반대편부터 탑레벨 도메인, 제2레벨 도메인, ... 이렇게 부른다.  
  (www.naver.com > com: 탑레벨 도메인, naver: 제2레벨)
- 이를 기반으로 하여 트리모양의 계층구조로 해당 ip주소를 찾는다.

## UDP프로토콜

DNS 서버는 UDP 프로토콜 위에서 작동한다.

> Why DNS servers use UDP protocol as the transport layer?  
[stackoverflow link](https://stackoverflow.com/questions/40063374/why-dns-uses-udp-as-the-transport-layer-protocol)  
> 1. UDP is much faster. TCP is slow as it requires 3 way handshake. The load on DNS servers is also an important factor. DNS servers (since they use UDP) don’t have to keep connections.  
UDP가 TCP보다 빠르다. 3-way handshaking 과정을 통한 커넥션 확보 과정을 거치지 않아도 된다. DNS는 사실 커넥션을 유지할 필요가 없기도 하다.
> 2. DNS requests are generally very small and fit well within UDP segments.  
DNS 요청 정보내용은 아주 작아서 UDP 세그먼트에 잘 들어간다.
> 3. UDP is not reliable, but reliability can be added on application layer. An application can use UDP and can be reliable by using timeout and resend at application layer.  
UDP는 신뢰성에 있어서 떨어지지만 충분히 application layer에서 보완할 수 있다.
