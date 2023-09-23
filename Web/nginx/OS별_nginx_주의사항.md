# OS 별 nginx 설정시 주의사항


## Debian

nginx -t 실행시 아래와 같은 에러 발생
```shell
nginx: [emerg] getpwnam("nginx") failed in /etc/nginx/nginx.conf:1
nginx: configuration file /etc/nginx/nginx.conf test failed

$ sudo useradd -r nginx
```
위와 같이 useradd로 해결([관련 issue 링크](https://github.com/NginxProxyManager/nginx-proxy-manager/issues/398))