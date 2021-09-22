# Session과 Cookie

- HTTP 프로토콜은 기본적으로 `connectionless`, `stateless`한 성격을 가진다.
- `connectionless`
  - TCP 방식에 의해 3-way, 4-way handshaking을 통한 연결 설정과 해제가 이루어짐
  - 연결을 하고 요청에 대한 응답 후에 연결을 끊어버림
- `stateless`
  - connectionless와 같이 언급되는 내용
  - 연결을 유지하지 않고 연결되어있는 동안의 상태정보를 저장하지 않기에 연결이 끊어지면 상태정보가 사라짐
- 두 성격으로 인해 쿠키와 세션 관리의 필요성이 생김

## Cookie

- 웹 브라우저에 저장되는 데이터(정확히는 브라우저를 사용하는 컴퓨터)
- 브라우저마다 저장되는 쿠키 데이터는 다르다.
- 서버는 브라우저마다 다른 사용자로 인식
- `Set-Cookie` Header key를 이용해 서버에서 브라우저 쿠키에 저장할 내용을 전달
- `Cookie` Header key를 이용해 브라우저에서 서버로 쿠키 데이터를 전달

```http
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry
```
```http
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```
(출처: https://cjh5414.github.io/cookie-and-session/)

- Session Cookie
  - 브라우저가 종료될 때 같이 제거되는 쿠키
- Permanent Cookie
  - `Expires`, `Max-Age` 옵션을 통해 `Set-Cookie` Header에서 설정 가능
  - 해당 옵션이 설정되면 브라우저가 종료되어도 쿠키가 유지된다.
- secure: HTTPS 프로토콜 상의 암호화된 요청에 대해서만 응답해주는 옵션
- httpOnly: cross-site 스크립팅 공격 방지. JS `document.cookie` api 접근을 방지
- domain, path: cookie의 scope 설정

### 용도
- ID 저장, 로그인 상태 유지
- 다시 보지 않기 (7일간 다시 보지않기 설정 등)
- 웹 페이지 사이드 광고에서 최근 검색한 상품들 기준으로 추천하는 용도로 사용
- 장바구니

## Session

- 서버에 저장되는 데이터(서버 자원 사용)
- 인증관련 민감한 데이터를 다룰 때 세션 이용
- 서버에서 `JSESSIONID` 값을 쿠키에 저장해서 브라우저에 전달
  - 해당 session id가 브라우저가 특정 세션에 접근할 수 있는 식별자 역할을 한다.