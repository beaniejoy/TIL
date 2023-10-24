# Cookie


## 쿠키 설정

### Domain
- domain으로 지정한 주소에 대해서만 서버로부터 쿠키를 받을 수 있고, 서버로 전송할 수 있다.

```
Set-cookie: beanie=joy; domain="beaniejoy.dev";
```

### HttpOnly
- httpOnly로 지정한 cookie에 대해서는 front 단에서 js로 document.cookie에 접근 불가능하게 된다.
- 즉, XSS(Cross-Site Scripting)공격을 방지해준다.
> This can help to prevent Cross-Site Scripting (XSS) attacks, where an attacker can inject malicious JavaScript code into a website that can then be executed by the victim’s browser.
- 하지만 CSRF(Cros-Site Request Forgery)에는 취약하다
  - 요청보낼때마다 해당 쿠키도 같이 보내지기 때문

### SameSite

```
Set-cookie: beanie=joy; domain="beaniejoy.dev" SameSite=Strict(or Lax);
```
- Strict
  - 크로스 사이트 요청에는 항상 전송되지 않는다. 서드 파티 쿠키는 전송되지 않고, 퍼스트 파티 쿠키만 전송됨
- Lax
  - Strict와 기본적으로 같지만, 예외사항을 남겨둠
```
Link	<a href=”…”></a>	Normal, Lax
Perender	<link rel=”prerender” href=”..”/>	Normal, Lax
Form GET	<form method=”GET” action=”…”>	Normal, Lax
```
- 위 사항을 예외사항으로 남겨둔다.
- 관련 좋은 글
  - https://seob.dev/posts/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%EC%BF%A0%ED%82%A4%EC%99%80-SameSite-%EC%86%8D%EC%84%B1/
  - https://www.invicti.com/blog/web-security/same-site-cookie-attribute-prevent-cross-site-request-forgery/


## 인증 token 관련

- [refresh_token을 httpOnly, secure 쿠키에 넣는 경우](https://velog.io/@yaytomato/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%90%EC%84%9C-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0)   
refresh_token 사용해서 access_token을 받아서 로컬변수로 사용한다는데 결국 XSS 취약한 것 같은데,,,
- refresh_token을 `SameSite=Strict`로 설정하는 경우  
이런 경우 Authorization Server의 주소와 프론트 주소가 같아야 하는데 같지 않은 경우가 대다수  
(front 따로 인증 서버 따로 두기 때문에 도메인 주소가 다를 가능성이 아주 큼)  
(refresh_token을 DB에 저장하는 경우 IO 부하, 메모리에 저장하는 경우도 어쨋든 삭제하고 저장하는 횟수가 늘어남)
- 결국 인증서버에서 CORS 설정을 통해 특정 도메인에 대해서만 허용하는 방식?  
  - 이부분도 생각해봐야될 듯, 아니면 특정 도메인(ip 주소)에 대해 방화벽 처리해서 특정 주소만 허용하는 식으로,,,
