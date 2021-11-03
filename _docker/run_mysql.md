# Docker를 이용한 mysql 서버 구성하기

```shell
$ docker run --name <CONTAINER_NAME> -e MYSQL_ROOT_PASSWORD=<PASSWORD> -d -p 3306:3306 mysql:latest
```
- docker를 이용해 root의 password 설정후 3306 port mapping 설정

```sql
> CREATE SCHEMA `database_name` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci
> USE `database_name`
```
- mysql docker container 실행 후 컨테이너 내부에서 스키마 실행