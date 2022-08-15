# Nginx, Application 연동 설정

- 개인 프로젝트에 대한 nginx 연동 테스트
- 추후 클라우드 서버까지 빌드, 배포 자동화까지 구축해볼 것
  - nginx(ec2): 미리 설정
  - app(ec2): 빌드 및 배포 자동화 프로세스 구축

## 목표
1. 로컬에서 nginx 설치 후 app 연동을 위한 conf 설정
   - app 빌드 후 연동(이후 테스트까지)
2. jenkins 빌드 자동화
3. 클라우드 서버에 nginx 설정
4. 클라우드 서버에 app 배포까지 자동화 후 nginx 연동 테스트

<br>

## 1. 로컬 nginx 설정
```bash
$ brew install nginx
```
- mac OS 기준
- brew를 이용한 nginx 새로 설치

### nginx 설정 파일 변경
```bash
vim /usr/local/etc/nginx/nginx.conf
```
- 본인 mac 기준 nginx 기본 설정 위치
- 설정 파일 수정 필요

```bash
server {
    # listen 8080;
    listen 80;

    #...

    # proxy to application listening on 127.0.0.1:8080 (Spring Boot application)
    location / {
        #root   html;
        #index  index.html index.htm;
        proxy_pass http://localhost:8080;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
    }
}
```
- 기본 포트가 8080이면 80으로 바꿀 것
- `proxy_pass`: `/` 경로로 들어오면 다음 경로로 pass 건네준다는 의미
- 그 외의 proxy를 위한 header 설정

### nginx 실행

```bash
$ brew services start nginx
$ brew services restart nginx
```
- application(`:8080`) 실행한 상태로 `http://localhost/cafes` 호출
- `80` -> `8080`(app)으로 전달되어 해당 응답값을 받아오는 것을 확인할 수 있다.

<br>
