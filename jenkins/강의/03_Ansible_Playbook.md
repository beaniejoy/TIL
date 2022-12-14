# Ansible Playbook 다루기

- Ansible Playbook을 활용한 여러가지 실행 테스트 해보기

<br>

## :pushpin: hosts 파일 설정해보기
- `first-playbook.yml` 작성하기
```yml
---
- name: Add an ansible hosts
  hosts: localhost
  tasks:
    - name: Add an ansible hosts
      blockinfile:
        path: /etc/ansible/hosts
        block: |
          [mygroup]
          172.17.0.5
```
```shell
$ ansible-playbook first-playbook.yml
```
ansible은 기본적으로 멱등성 성질을 가지고 있기 때문에 위의 명령을 여러번 동작시켜도 정상적으로 완료되지만,  
`/etc/ansible/hosts` 파일에는 처음 한 번 적용된 것만 유지되고 나머지는 무시된다.

<br>

## :pushpin: host들에 파일 복사해보기

```yml
---
- name: Ansible Copy  Example Local to Remote
  hosts: devops
  tasks:
    - name: copying file with playbook
      copy:
        src: ~/sample.txt
        dest: /tmp
        owner: root
        mode: 0644
```

<br>

## :pushpin: Tomcat 9 version 다운받아보기
```yml
---
- name: Download Tomcat9 from tomcat.apache.org
  hosts: devops
  tasks:
    - name: Create a Directory /opt/tomcat9
      file:
        path: /opt/tomcat9
        state: directory
        mode: 0755
    - name: Download Tomcat9 using get_url
      get_url:
        url: https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.70/bin/apache-tomcat-9.0.70.tar.gz
        dest: /opt/tomcat9
        mode: 0755
        checksum: sha512:https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.70/bin/apache-tomcat-9.0.70.tar.gz.sha512
```
- `checksum`은 검증 목적으로 사용됨
  - [ansible get_url module doc](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html)
    - 여기에 checksum parameter에 어떻게 기입해야되는지 나온다.
  - [Tomcat 9 version download](https://tomcat.apache.org/download-90.cgi)
    - 여기에 checksum 내용이 나온다.
- 최근에 ansible playbook에서 모듀리 적용시 `ansible.builtin.get_url` 이런식으로 사용하는 듯
