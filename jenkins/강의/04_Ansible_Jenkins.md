# Ansible 활용한 Jenkins 배포

## :pushpin: 현재까지의 서버 구성 상황

```
jenkins-server: 172.17.0.2
ansible-server: 172.17.0.3
docker-server: 172.17.0.4
```
- 위 세 개의 docker container들은 하나의 network로 묶여 있는 형태
- 일종의 내 맥북 localhost 내부에 private ip 구성된 상황
- localhost에서 docker로 ssh 접속할 때는 port forwarding을 통해 접속
```shell
10022:22 port forwarding 된 상황
$ ssh root@localhost -p 10022
```

<br>

## :pushpin: Jenkins 구성해보기

기존 Jenkins CI/CD 과정은
```
git > Jenkins build > copy .war to docker-server > docker build
```
여기서 문제는 docker-server에 copy해서 docker를 통해 run을 하게 되는데  
만약 기존에 이미 같은 포트로 실행하고 있는 컨테이너가 있으면 오류가 나면서 jenkins build가 실패하게 된다.  
이를 Ansible을 같이 활용해서 해결해보고자 하는게 이번 강의 핵심

<br>

## :pushpin: Jenkins 설정
- jenkins 설정 > 환경 설정

<br>

## :pushpin: Playbook 작성하기
`ansible-server`에서 작성해야 함
```yml
---
- hosts: all
  tasks:
    - name: build a docker image with deployed war file
      command: docker build -t cicd-project-ansible .
      args:
        chdir: /root
    - name: create a container using cicd-project-ansible image
      command: docker run -d --name my_cicd_project -p 8080:8080 cicd-project-ansible
```
docker build 후, run 실행

<br>

## :pushpin: Jenkins 프로젝트 설정
```shell
ansible-playbook -i hosts first-devops-playbook.yml
```
- Jenkins 프로젝트 생성 후 빌드 후 조치에 설정 필요
  - **Exec command**에 ssh transfer 이후 실행할 명령어 설정해야함
