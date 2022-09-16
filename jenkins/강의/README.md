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

