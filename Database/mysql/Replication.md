# Replication

docker 기반 로컬 mysql replication 구성했던 내용 기록

## :pushpin: docker로 구성하는 방법

- https://dkswnkk.tistory.com/725 참고


## 주의할 점
replication 구성후 replica db에 replica 모드가 활성화되어 있는 상태에서 source db에 user를 추가하거나 권한을 부여하는 등 서비스 테이블 관련 쿼리 이외에 설정을 위한 쿼리 실행을 하면 안된다.

source는 query ok인데 replica에서 실패하는 경우 그 이후로 replica 동작이 진행이 안된다. (binary log파일에 replica에는 적용 안되는 내용이 기록되어 있기 때문)


## :pushpin: replica 구성시 postition 설정

master에 개발용 특정 테이블 권한만 부여한 user를 적용하고 나서 repllica db를 구성하려고 할 때 position을 설정하지 않으면 master에서 설정했던 내용까지 replica에 적용되는 것을 볼 수 있다.

```sql
-- source db
SHOW MASTER STATUS;
```
source db에서 binary log 파일과 position을 확인할 수 있음.

```
+------------------+----------+--------------+------------------+-------------------------------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set                         |
+------------------+----------+--------------+------------------+-------------------------------------------+
| mysql-bin.000005 |      197 |              |                  | d683e03f-bd0b-11ee-82e8-0242ac150002:1-46 |
+------------------+----------+--------------+------------------+-------------------------------------------+
```
File에는 실행된 마지막 로그 파일, position은 해당 로그 파일 내에서 마지막 위치지점을 의미

```sql
-- replica db (docker compose 환경)
CHANGE MASTER TO MASTER_HOST='source-db', \ 
	MASTER_USER='repl', \
	MASTER_PASSWORD='repl', \
	MASTER_LOG_FILE='mysql-bin.000005', \
	MASTER_LOG_POS='197';
```
replication 실행하기 전에 master 정보 업데이트 할 때 해당 log file과 position 정보를 입력.

```sql
START REPLICA; -- (혹은 SLAVE)
```

> 대신 GTID 기반이 아닌 기존 log file, position 방식으로 replica가 동작이 되는데 이부분에 대해서 좀 더 찾아봐야 할 듯 (GTID 기반이면서 특정 트랜잭션 이후로 replica 실행하는 방법에 대해서)

## :pushpin: docker mysql 관련

fedora os인 경우
```shell
$ microdnf install vim
```
(https://github.com/docker-library/mysql/issues/960 참고)