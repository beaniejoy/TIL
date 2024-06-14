# OAuth 2.0 grant type

<br>

## Authorization Code Grant Type

> 권한 부여 코드 승인 방식
> grant_type=authorization_code

1. 사용자(Resource Owner) 접속
2. Client(App)에서 authorization code(권한 부여 코드) 요청
3. 인가 서버(Authorization Server)에서 사용자에게 로그인 페이지 제공
4. 사용자 로그인
5. 인증 성공시 인가 서버는 authorization code를 Client에게 부여
6. Client는 Access Token 교환 요청
   - grant_type=authorization_code
   - code: authorization code
   - client_id
   - client_secret: 인가서버에 등록된 클라이언트 시크릿 정보
   - redirect_uri (권한부여코드 요청시 설정한 것이 있으면 똑같아야 한다.)

- **장점**
  - 액세스 토큰 요청시 클라이언트 시크릿 정보로 클라이언트에 대한 인증이 이루어지기 때문에 해당 코드를 가로챌 여지가 줄어듬 
  (서버 측에서 교환)
  - 액세스 토큰 자체가 사용자 또는 브라우저에 표시되지 않고 애플리케이션으로 다시 전달되는 가장 안전한 방법
  - Back Channel에서 access token 부여 절차가 발생해서 안전

<br>

## Implicit Grant Type

> 암묵적 승인 방식
> response_type=token

1. 사용자 접속
2. Client(Web App)이 인가 서버에 access token 요청
3. 인가 서버 사용자 로그인 제공
4. 사용자 로그인 인증
5. Web Client에 url fragment(#) 방식으로 노출된 형태로 access token 부여

간단하다는 장점이 있지만 브라우저에 토큰이 노출된다는 점에서 보안상 허점이 존재

<br>

## Resource Owner Password Credentials Grant Type

> 패스워드 자격증명 승인 방식
> grant_type=password

1. 사용자 접속
2. Client(back channel)는 사용자로부터 username, password 정보를 받음
3. 이를 기반으로 인가 서버에 access token 요청
4. 인가 서버에서 인증 완료시 access, refresh token 받음

Web Application이 사용자의 usernmae, password를 알고 있다는 점에서 취약성이 존재
아무리 인증된 애플리케이션이라 해도 언제든지 해당 고객정보를 가지고 인가 서버에 사용자 정보를 받아볼 수 있다.

<br>

## Client Credentials Grant Type

> 클라이언트 자격증명 승인 방식
> grant_type=client_credentials

Resource Owner, Client가 하나로 합쳐진 개념
Client가 권한을 위임받는 형식으로 이루어지지 않음
(바로 access token 요청)

server to server에서만 사용 가능, ui가 존재하지 않음
`client_id`, `client_secret` 두 개 정보만 필요
(가장 간단한 형태)


## Refresh Token Grant Type

> 리프레쉬 토큰 승인 방식
> grant_type=refresh_token

최초 액세스 토큰 발급될 때 같이 발급, 이후 엑세스 토큰 만료되어도 리프레쉬 토큰으로 액세스 토큰 재발급 가능
(처음부터 다시 인증 과정을 거치지 않아도 된다.)

refresh token 요청을 보내면 새로운 access, refresh token 둘 다 발급 받음

<br>

## PKCE-enhanced Authorization Code Grant Type

> PKCE(Proof Key for Code Exchange, RFC-6749)
> 