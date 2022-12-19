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
    - name: create a  container using cicd-project-ansible image
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
- Poll SCM 설정
  - `* * * * *` 으로 설정해서 1분마다 git repository에 새로운 커밋이 있나 polling check

<br>

## :pushpin: Playbook 파일 내용 수정
cicd-project repository의 `index.jsp` 내용을 바꿔서 커밋하면 Poll SCM 설정에 의해 1분뒤 바뀐 커밋을 감지  
Jenkins에서 받아와서 빌드 후 ansible-server로 war 파일 보내고 playbook 파일 실행을 하게 되는데 에러 발생  
이미 기존에 실행되고 있는 docker container가 있기 때문에 같은 포트로 새로운 container를 실행할 수 없는 것이다.  
이에 대해서 playbook 파일에 container 실행 중지, 삭제, image 삭제 내용을 전처리 작업으로 추가 설정해주어야 한다.

```yml
---
- hosts: all
  tasks:
    - name: stop current running container
      command: docker stop my_cicd_project
      ignore_errors: yes

    - name: remove stopped container
      command docker rm my_cicd_project
      ignore_errors: yes

    - name: remove current docker image
      command: docker rmi cicd-project-ansible
      ignore_errors: yes

    - name: build a docker image with deployed war file
      command: docker build -t cicd-project-ansible .
      args:
        chdir: /root

    - name: create a container using cicd-project-ansible image
      command: docker run -d --name my_cicd_project -p 8080:8080 cicd-project-ansible
```

<br>

## :pushpin: Ansible 이용한 Docker 이미지 관리

```shell
$ docker login
```
docker 계정 인증 먼저 진행

```yml
(create-cicd-devops-image.yml)
---
- hosts: all
  tasks:
    - name: create a docker image with deployed war file
      command: docker build -t hbleejoy/cicd-project-ansible .
      args:
        chdir: /root

    - name: push the image on Docker Hub
      command: docker push hbleejoy/cicd-project-ansible

    - name: remove the docker image from the ansible server
      command: docker rmi hbleejoy/cicd-project-ansible
      ignore_errors: yes
```
docker image (tag) 생성 이후 Docker Hub에 push하는 ansible playbook file 작성
위 내용 테스트가 잘 진행되었으면 다음 내용 수행
```yml
(create-cicd-devops-container.yml)
---
- hosts: all
  tasks:
    - name: stop current running container
      command: docker stop my_cicd_project
      ignore_errors: yes

    - name: remove stopped container
      command: docker rm my_cicd_project
      ignore_errors: yes

    - name: remove current docker image
      command: docker rmi hbleejoy/cicd-project-ansible
      ignore_errors: yes

    - name: pull the newest docker image from Docker Hub
      command: docker pull hbleejoy/cicd-project-ansible

    - name: create a container using cicd-project-ansible image
      command: docker run -d --name my_cicd_project -p 8080:8080 hbleejoy/cicd-project-ansible
```
기존 `cicd-project-ansible` image로 build 후 run 했던 내용을  
Docker Hub로부터 pull 받고 run하는 내용으로 수정

```shell
$ ansible-playbook -i hosts create-cicd-devops-image.yml --limit 172.17.0.3
```
ansible-server(172.17.0.3)로 한정해서  ansible 잘 실행되는지 확인

```shell
$ ansible-playbook -i hosts create-cicd-devops-container.yml --limit 172.17.0.4
```
이번에는 docker-server(172.17.0.4)에 container playbook 파일내용을 적용해보자  
docker stop, rm, rmi 작업이 오류가 나올 수 있는데 정상적인 오류다.  
(docker-server에는 images, process가 아무것도 없기 때문에 발생할 수 있는 오류)

### 정리
```shell
$ ansible-playbook -i hosts create-cicd-devops-image.yml --limit 172.17.0.3
$ ansible-playbook -i hosts create-cicd-devops-container.yml --limit 172.17.0.4
```
- ansible-server에서 먼저 docker image 생성 후 Docker Hub에 push
- docker-server에서는 push 된 docker image를 pull 받아오고 해당 image로 실행을 한다.
- 이걸 jenkins 빌드 후 조치에서 Exec command에 설정가능