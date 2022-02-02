# TCP Connection

- 인터넷 연결과정

## 📌 인터넷 연결 과정

[Go to Top](https://github.com/beaniejoy/TIL/blob/main/HTTP/Connection/TCP_Connection.md)

> 브라우저에서 `beaniejoy.io` 에 접속하면 어떤 과정으로 접속이 이루어질까?  
> 전체 URL은 `http://beaniejoy.io:80/index.html` 라고 가정

1. 웹브라우저가 `http://beaniejoy.io` 로 웹페이지를 요청해야 한다는 것을 인지
   - 브라우저가 전체 URL에서 호스트 명을 추출
2. 웹브라우저가 DNS에 요청해서 호스트명에 대한 서버의 ip 추출 
   - [이름 해결과정](https://goodgid.github.io/Server-DNS/#dns-%EC%84%9C%EB%B2%84%EB%8A%94-2%EC%A2%85%EB%A5%98/)
   - DNS서버는 UDP 통신
3. 웹브라우저가 전체 URL에서 포트 번호(`80`) 획득
4. 웹브라우저가 DNS로부터 받은 ip주소와 포트 번호를 가지고 HTTP 메세지를 생성
   - header, body 내용 담아서 구성. 
   - 여기에는 타겟 주소와 HTTP Method, 요청 페이지 등을 담음.
5. 💡 웹브라우저는 `ip주소 + 포트번호`를 통해 서버와 최초 커낵션을 생성  
   - **[TCP 3way handshaking](https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake)** 방식을 통해 커낵션 생성
   - [패킷으로 보는 초간략 HTTP 통신과정](https://codetronik.tistory.com/88)
6. 통신 연결이 완료되면 HTTP 요청을 보낸다. (`GET HTTP/1.1`)
7. 서버는 메세지를 받고 해당 HTTP 요청을 해석해서 원하는 페이지를 찾는다.
   - 해당 리소스가 존재하면 segment로 나눠서 전송
8. 해당 리소스의 전송이 완료 되면 응답 메시지 전송
   - HTTP status code(여기서는 200)와 함께 HTTP 응답 매세지를 만들고 클라이언트에 전송
9.  💡 **[TCP 4way handshaking](https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake)** 방식으로 커낵션을 종료
