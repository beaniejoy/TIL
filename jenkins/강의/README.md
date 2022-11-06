# Jenkins를 이용한 CI/CD Pipeline 구축

## DevOps, CI/CD 이해

- waterfall
  - 순차적으로 진행
  - 요구사항정의, 분석/설계, 구현, 테스트, 운영 단계로 순차 진행
  - 고객의 니즈를 즉각 반영하기 힘들다. (시간비용이 큼)
- agile
  - 프로젝트의 생명주기 자체를 반복적으로 짧게 운영
  - 고객의 니즈를 즉각 반영 가능

### Cloud Native Application
- 4가지 특징
  - Microservices
  - Containers
  - DevOps
  - CI/CD


### docker로 jenkins 실행해보기
```bash
$ docker run -d -p 8088:8080 -v ~/Dev/tools/jenkins_home/:/var/jenkins_home -p 50000:50000 --name jenkins-server --restart=on-failure jenkins/jenkins
```
- https://github.com/jenkinsci/docker/blob/master/README.md

<br>

## PollSCM 
- git 소스코드 관리로 git repository와 연동해서 해당 repo의 commit 변경 내역을 주기적으로 jenkins에서 확인해준다. (polling)
- 변경내용이 있을시 해당 내용을 받아와서 jenkins 빌드 자동 실행
- jenkins PollSCM 설정 옵션에서 `schedule` 설정을 할 수 있다.

<br>

## SSH Server 연동하기
- jenkins에서 빌드한 파일을 원격 ssh server로 전송할 수 있다.
- jenkins plugin - `Publish over SSH` 사용

### 순서
1. war 파일 생성 및 SSH 이용한 원격 서버(강의에서는 로컬 내 다른 ssh server docker container로)로 파일 복사
2. 원격 서버에서 `Dockerfile` + `war file` 가지고 docker image build
3. docker image -> docker container 생성

### 구조도

```
[Local 환경]
- docker-server(SSH server) > 8081:8080
  - 서버 내부 docker(tomcat) > 8080:8080
- jenkins: 8080:8080
```