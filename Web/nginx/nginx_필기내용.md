# nginx 기록 내용

<br>

## References
- [Load balancing Nginx doc](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)

<br>

## proxy set header

proxy_set_header가 하나라도 설정되어 있으면 상위 레벨에 있는 헤더정보들은 싹다 무시된다.
```conf
proxy_set_header foo foo;
proxy_set_header bar bar;

location /some/path {
  # 이 블럭에서 `proxy_set_header`가 사용되었기 때문에,
  # 상위 레벨의 `foo`와 `bar` 헤더가 모두 무시된다. (상속되지 않는다)
  proxy_set_header baz baz;
  proxy_pass http://localhost:8000;
}

####################################################

proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;

location /some/path {
  # 새 헤더 추가 (이 설정으로 상위 헤더가 무시된다)
  proxy_set_header foo foo;
  proxy_pass http://localhost:8000;
}

####################################################

# 이렇게 동위 레벨에 설정을 해야 한다.
location /some/path {
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header foo foo;
  proxy_pass http://localhost:8000;
}
```

<br>

## proxy connection 전달 주의 사항

```conf
location / {
  proxy_http_version                    1.1;
  proxy_set_header      Connection      "";
}
```
Nginx는 기본적으로 HTTP 요청을 upstream 서버로 프록시할 때 HTTP 버전을 1.0 바꿈.  
이 과정에서 Connection 헤더도 close로 바꿈