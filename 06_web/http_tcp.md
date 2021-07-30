# HTTP

- **HTTP통신**
  - Client가 요청을 보내는 경우에만 Server가 응답하는 단방향 통신
  - Server로부터 응답을 받은 후에는 연결이 바로 종료
  - 실시간 연결이 아니고, 필요한 경우에만 Server로 요청을 보내는 상황에 유리
  - 요청을 보내 Server의 응답을 기다리는 어플리케이션의 개발에 주로 사용
  - `TCP/IP`를 이용하는 application protocol
  - 비연결성 프로토콜(`stateless`)이기 때문에 cookie, session 개념이 등장
- **Socket 통신**
  - Server와 Client가 계속 연결을 유지하는 양방향 통신
  - Server와 Client가 실시간으로 데이터를 주고받는 상황이 필요한 경우에 사용
  - 실시간 동영상 Streaming이나 온라인 게임 등과 같은 경우에 자주 사용

## 📌 TCP, UDP

IP 프로토콜 위에서 작동하는 `Transport Layer`
  
**연결형 서비스**
- 송신자와 수신자 사이의 논리적인 연결을 확립 
- 패킷들의 순서가 맞지 않을 위험이 적음
- 오류 발생 시 재전송
- TCP  

**비연결형 서비스**
- 송신자와 수신자 사이에 연결을 확립 X
- 오류 확인을 하지 못하므로 신뢰성 없는 전송
- 연결확립에 걸리는 시간이 없어 전송 속도가 빠름
- IP, UDP

### TCP (Transmission Control Protocol)
- IP 프로토콜 위에서 연결형 서비스를 지원하는 전송 계층 프로토콜
인터넷 환경에서 기본으로 사용
- **연결형 서비스**, 가상 회선 방식
발신지, 수신지를 연결해 패킷을 전송하기 위한 논리적 경로를 설정
- 높은 신뢰성
`3-way, 4-way handshaking`을 통해 정확한 전송을 보장
- UDP보다 속도가 느리다.
- 전이중 방식(Full Duplex)의 양방향 가상 회선을 제공

### UDP (User Datagram Protocol)
- **비연결형 서비스** 제공
- 헤더와 전송 데이터에 대한 체크섬 기능을 제공
- [Best Effort](https://en.wikipedia.org/wiki/Best-effort_delivery) 전달 방식을 지원
- 전송한 데이터그램이 목적지까지 잘 도착했는지 확인하지 않기 때문에 신뢰성은 떨어진다.
- 데이터 처리가 빠르기 때문에 데이터 전송시간에 민감한 환경에서 UDP 사용  
(스트리밍 서비스, 실시간 중계, 게임 등, DNS 서버도 UDP를 사용)

## 📌 인터넷 연결 과정

> 브라우저에서 `beaniejoy.io` 에 접속하면 어떤 과정으로 접속이 이루어질까?

1. 웹브라우저가 `beaniejoy.io` 로 웹페이지를 요청해야 한다는 것을 인지
2. DNS에 요청해서 URL을 이용해 서버의 ip 추출 ([이름 해결과정](https://goodgid.github.io/Server-DNS/#dns-%EC%84%9C%EB%B2%84%EB%8A%94-2%EC%A2%85%EB%A5%98/) - UDP 통신)
3. DNS로부터 받은 ip주소를 가지고 HTTP 메세지를 만든다.  
(header, body 내용 담아서 구성. 여기에는 타겟 주소와 HTTP Method, 요청 페이지 등을 담는다.)
4. 💡 웹브라우저는 서버와 **[TCP 3way handshaking](https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake)** 방식을 통해 최초 커낵션을 생성  
([패킷으로 보는 초간략 HTTP 통신과정](https://codetronik.tistory.com/88))
5. 통신 연결이 완료되면 HTTP 요청을 보낸다. (GET / HTTP/1.1)
6. 서버는 메세지를 받고 해당 HTTP 요청을 해석해서 원하는 페이지를 찾는다. 해당 리소스가 존재하면 segment로 나눠서 전송
7. 해당 리로스의 전송이 완료 되면 HTTP status code(여기서는 200)와 함께 HTTP 응답 매세지를 만들고 클라이언트에 전송
8. 💡 **[TCP 4way handshaking](https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake)** 방식으로 커낵션을 종료

## 📌 HTTP Status Code

- `1xx`: Informational : 요청 정보 처리 중
- `2xx`: Success : 요청을 정상적으로 처리함
- `3xx`: Redirection : 요청을 완료하기 위해 추가 동작 필요
- `4xx`: Client Error : 서버가 요청을 이해하지 못함
- `5xx`: Server Error : 서버가 요청 처리 실패함

## 📌 HTTP 버전별 특징

### HTTP/0.9

- 요청은 단일 라인으로 구성
- GET 방식만 존재
- HTTP 헤더가 없어서 HTML 파일만 전송할 수 있었음
- 상태, 오류코드도 없었음

```
# Request (요청은 단일 라인)
GET /mypage.html

# Response (응답은 오직 파일 내용 자체로 구성)
<HTML>
A very simple HTML page
</HTML>
```

### HTTP/1.0

- 버전정보가 요청 사이로 전송(`GET /example.html HTTP/1.0`)
- 응답헤더에 상태 코드 라인도 생겼다. 요청에 대한 실패유무를 확인 가능
- 응답헤더 덕분에 HTML 문서 외의 다른 문서들을 전송하는 기능 추가
(Content-Type)
- 극도로 유연하고 확장 가능한 프로토콜로 만들어 줌
- `단기 커넥션`

### HTTP/1.1

- 커넥션의 재사용 가능
- 파이프라이닝 추가. 첫번째 요청에 대한 응답이 완전히 전송되기 전에 두번째 요청 전송을 가능케 함
- `영속적인 커넥션(keep-alive 커넥션)`

### HTTP/2.0

- 기존에 Plain Text(평문)를 사용하고, 개행으로 구별되던 `HTTP/1.x` 프로토콜과 달리, `HTTP/2.0`에서는 바이너리 포멧으로 인코딩 된 Message, Frame으로 구성된다.
- HTTP Header Data Compression (HTTP 헤더 데이터 압축)

## 📌 HTTP, HTTPS

- `HTTP`: **H**yper**T**ext **T**ransfer **P**rotocol
- `HTTPS`: **H**yper**T**ext **T**ransfer **P**rotocol over **S**ecure

HTTP는 서버에 요청을 보내고 그 응답데이터를 브라우저에 보내는 방식으로 작동하는데 중간에 해커가 이를 가로채면 데이터가 쉽게 도난당할 수 있다는 문제점이 있었다.  

HTTPS는 이러한 도난의 문제를 극복하고자 SSL(보안 소켓 계층)을 사용함으로써 보안을 강화한 형태로 요청, 응답을 주고받을 수 있게 했습니다.

`SSL`: 서버와 브라우저 사이에 안전하게 암호화된 연결을 만들 수 있게 도와주고, 서버 브라우저가 민감한 정보를 주고받을 때 이것이 도난당하는 것을 막아준다.

HTTPS의 핵심은 개인키/공개키 암호화, 복호화 방식에 있다. ([링크 참고](https://mangkyu.tistory.com/98))

## 📌 Connection

HTTP에서의 TCP 연결의 형태

![HTTP1_x_Connections](https://user-images.githubusercontent.com/41675375/127663046-e3c55e5a-3612-46d8-8ca5-7014d9281192.png)

- `단기 커넥션`
  - `HTTP/1.0`에서는 이러한 지속적인 TCP 커낵션의 효율을 사용하지 못해서 최적의 상태가 아님  
- `영속적인 커넥션`
  - TCP handshaking에서 비용이 발생하기는 하지만 TCP 커낵션은 지속적으로 연결되었을 때 부하에 맞춰 더욱 예열되어 더욱 효율적으로 작동
  - `HTTP/1.1`은 영속적인 커넥션 TCP handshaking와 커넥션 지속에 따른 효율 두마리 토끼를 다 잡을 수 있음 (`keep-alive`)
  - 영속적인 커넥션은 유휴 상태일 때에도 서버 리소스를 계속 사용하기 때문에 과부하 상태에서는 `DoS attack`에 취약할 수 있다.  
   (커넥션이 유휴 상태가 되자마자 비영속적 커넥션을 사용하는 것이 성능에 좋을 수 있음)
- `파이프라이닝`
  - 같은 영속적인 커넥션을 통해 응답을 기다리지 않고 요청을 연속적으로 보내는 기능
  - 이전에는 현재 요청에 대한 응답을 받고 그 이후 다음 요청 수행  
  → 네트워크 지연, 대역폭 제한에 걸려 다음 요청을 보내는데 상당한 delay 발생 가능
  - 파이프라이닝에서는 이전 응답을 기다리지 않고 요청을 연속적으로 보낼 수 있음
  - 하지만 실제로 이것을 지원하기에는 많은 프록시와 서버들에 제한이 존재
- [관련 설명 링크](https://developer.mozilla.org/ko/docs/Web/HTTP/Connection_management_in_HTTP_1.x)