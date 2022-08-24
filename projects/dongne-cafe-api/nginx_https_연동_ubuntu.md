# Nginx로 https 설정 test (ububtu)

- AWS
- nginx
- letsencrypt

<br>

## AWS EC2 설정

### Package Installation

```shell
$ sudo apt-get update
$ sudo apt-get install nginx letsencrypt
```

### Create Certificate
- HTTPS SSL을 위한 인증서 생성(`letsencrypt` 패키지)

```shell
$ sudo systemctl stop nginx
$ sudo systemctl status nginx
```
- nginx 작동 중지 후 상태 확인

```shell
$ sudo letsencrypt certonly --standalone -d <DOMAIN_NAME>
```
- `<domain name>`: AWS와 연결된 도메인 이름으로 할 것(구매한 도메인으로)

### nginx 설정

```shell
$ sudo vim /etc/nginx/sites-available/default
```
- default로 설정된 nginx 내용 HTTPS 연동하기

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    # SSL configuration
    listen 443 ssl;
    listen [::]:443 ssl;

    ssl_certificate /etc/letsencrypt/live/<DOMAIN_NAME>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<DOMAIN_NAME>/privkey.pem;

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout 10m;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
}

#...

server {
    listen 80;
    listen [::]:80;

    server_name <DOMAIN_NAME>;

    return 301 https://$host$request_uri;
}
```
- 기본 설정에 443(HTTPS)포트에 대한 ssl 설정
- 생성된 certificate를 연결
- 80포트에 대해서 redirect(301) 처리할 것

<br>

## Nginx 관련 패키지 제거

```shell
$ sudo apt-get --purge remove nginx nginx-*
$ sudo apt-get install nginx
```
- 혹시 nginx 다시 설치하고자 패키지 제거시 `nginx` 뿐만아니라 `nginx-*`도 제거해야함
