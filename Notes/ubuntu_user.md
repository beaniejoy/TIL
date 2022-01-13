# user

- user 생성 및 sudo 그룹 추가
```bash
$ sudo useradd newuser

$ sudo usermod -aG sudo newuser

$ sudo -u newuser /bin/bash
```
- user 전환
```bash
$ sudo su - u newuser
```

- user list 조회
```bash
$ cat /etc/passwd
```
