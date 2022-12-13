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