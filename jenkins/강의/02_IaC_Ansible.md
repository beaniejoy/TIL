# Infrastructure as Code & Ansible 적용

## 📌 개념

- 스크립트(코드)를 통해 관리 및 프로비저닝
- 즉 코드를 통해 시스템, 하드웨어, 인터페이스 구성정보를 관리
- 베어 메탈 서버 등의 물리서버 및 가상 머신과 관련된 구성 리소스를 관리
  - 베어 메탈 서버: 아무것도 설치되어 있지 않는 순수한 상태의 서버

### 장점
- 코드로 관리할 수 있기 때문에 자동화 가능
- 버전 관리를 통한 리소스 관리가 가능해진다.
- Agentless
  - 대상 서버에 ansible을 위한 agent를 따로 설치하지 않아도 된다. 
  - python의 SSH 기능을 사용해서 원격 서버와 연동

<br>

## :pushpin: 구성

설치
```shell
$ yum install ansible
$ ansible --version
```
- 환경설정 파일: `/etc/ansible/ansible.cfg`
- Ansible에서 접속하는 호스트 목록: `/etc/ansible/hosts`
  - 대상 서버 목록

### docker 구성

```shell
$ docker run --privileged \
  -itd --name ansible-server \
  -p 20022:22 -p 9090:8080 \
  -e container=docker \
  -v /sys/fs/cgroup:/sys/fs/cgroup \
  --cgroupns=host \
  edowon0623/ansible:latest \
  /usr/sbin/init

$ ssh root@localhost -p 20022
혹은
$ docker exec -it ansible-server bash
```

### ssh 설정

ansible-server 안에서 docker-server에 원격 접속을 하려고 할 때
```shell
$ ssh root@172.17.0.4
```
이렇게 하면 되는데 매번 해당 서버의 비밀번호를 치고 들어가야 한다는 단점이 있다.  

ssh key를 생성하고 docker-server에도 배포하는 방식으로 해서 키 교환하여 ssh 접속시 비밀번호 안쳐도 되게끔 구성해보자
```shell
$ ssh-keygen
$ ssh-copy-id root@172.17.0.4
```
- `ssh-keygen`: 해당 로컬호스트에 공용키, 개인키 쌍 생성
- `ssh-copy-id`
  - 로컬호스트의 공용 키(public key)를 원격 호스트의 `authorized_keys` 파일에 복사
  - `ssh-copy-id` 또한 알맞은 권한을 원격 호스트의 홈, `~/.ssh`, `~/.ssh/authorized_keys`에 부여

<br>

## :pushpin: Ansible 실행해보기

실행시 옵션내용
- `-i`(`--inventory-file`)
  - 적용될 호스트들에 대한 파일 정보 설정
  - 기본적으로 `/etc/ansible/hosts` 를 바라보는데 해당 옵션을 설정하면 해당 파일로 호스트들이 설정됨
- `-m`(`--module-name`): 모듈 선택
- `-k`(`--ask-pass`): 관리자 암호 요청
- `-K`(`--ask-become-pass`): 관리자 권한 상승
- `--list-hosts`: 적용되는 호스트 목록

ansible의 멱등성 특징
- 같은 설정을 여러 번 적용하더라도 결과가 달라지지 않는 성질

```shell
$ echo -e "[mygroup]\n172.20.10.11" >> /etc/ansible/hosts
```
위 명령어를 실행했을 때 hosts 파일 내용 아래에 append 되어 계속 붙여질 것이다.  
그런데 ansible을 통해 해당 명령어를 실행하면 한 번만 실행하게 된다.