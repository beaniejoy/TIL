# Chap 2. 설치와 설정

버전은 메인 버전에서 패치가 15번 이상된 버전을 사용하는 것이 좋다.  
(ex. `8.0.15` 이상)

<br>

## :pushpin: Installation 

### mac OS 환경에서 MySQL 설치

```shell
$ sudo /usr/local/mysql/support-files/mysql.server start
$ sudo /usr/local/mysql/support-files/mysql.server status
```
mysql 실행

```shell
# MySQL
export PATH=/usr/local/mysql/bin:$PATH
```
`~/.zshrc` 파일에 mysql 실행경로 설정

#### 디렉토리 구조
기본적을 `/usr/local/mysql` 에서 관리
```
- data
- lib
- share
- include
```

### CentOS(linux) 환경에서 설치
virtual box 설치 후 user 추가
```shell
$ useradd test
$ passwd test

$ su - root
$ usermod -aG wheel test
```
wheel 그룹의 맴버는 sudo 권한 주어짐  

```shell 
$ scp -P 22222 mysql80-community-release-el8-4.noarch.rpm root@localhost:/home/test
```
- https://dev.mysql.com/downloads/repo/yum/ 여기서 mysql8 redhat linux8 버전 rpm 파일 다운
- Virtual Box로 설치한 CentOS 서버에 scp로 전달

```shell
$ sudo rpm -Uvh mysql80-community-release-el8-4.noarch.rpm
```
rpm 패키지 지정
```shell
$ sudo yum search mysql-community
$ sudo yum --showduplicates list mysql-community-server
```
yum package 리스트 받아오는데 mysql-community-server 패키지가 없을 수 있다.  

```shell
$ sudo yum module disable mysql
```
mysql module disable 처리한 이후 mysql-community-server 패키지가 나올 것이다.  
yum list를 통해 mysql-community-server 원하는 버전(8.0.15 이상)으로 설치 진행

```shell
$ sudo yum install mysql-community-server-8.0.32
```

#### my.cnf 디렉토리 위치 변경 이후 오류
```
mysqld: Can't create/write to file '/data/mytmp/ib3cEBLV' (OS errno 13 - Permission denied)
[ERROR] [MY-012576] [InnoDB] Unable to create temporary file inside "/data/mytmp"; errno: 13
```
SELinux와 관계 있는 것 같음(뭔지는 찾아봐야함)  
```shell
$ semanage fcontext -a -t mysqld_db_t "/data/mytmp(/.*)?"
$ restorecon -R /data/mytmp
```
이렇게 하고 mysqld 시작하면 성공했음


<br>

## :pushpin: MySQL 서버 업그레이드

- 데이터 딕셔너리(data dictionary)
```
- information_schema
- mysql
- sys
- performance_schema
```

<br>

## :pushpin: 시스템 변수 (System Variables)

```shell
$ mysqld --verbose --help | grep -A 10 -B 10 'Default options'
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /usr/local/mysql/etc/my.cnf ~/.my.cnf
```
설정 파일 경로 확인(mac os 환경에서는 정확하지는 않은듯)  

### 글로벌, 세션 변수

- 글로벌 변수
  - mysql server instance 전역적으로 영향을 미치는 시스템 변수
  - mysql server 자체에 대한 설정인 경우가 대다수
  - ex. `innodb_buffer_pool_size`, `key_buffer_size` 등
- 세션 변수
  - 개별 커넥션 단위로 다른 값으로 변경할 수 있는 시스템 변수
  - mysql server 설정파일에 초기값 명시할 수 없음(X)
  - `autocommit`
- Both
  - 글로벌, 세션 둘다 해당
  - mysql server 설정파일에 초기값 설정 가능
  - 커넥션 생성부터 해당 커넥션에 초기값 설정(해당 커넥션에서만 유효)

### 동적, 정적 변수
mysql 서버 기동 중인 상태에서 변경 가능 여부에 따라 나뉨

- 동적 변수: 기동 중인 상태에서 변경 가능
  - `SET` 으로 시스템 변수 변경 가능(재시작 필요 X), 서버 재시작시 초기화
  - `SET PERSIST`로 별도 설정파일로 기록해서 재시작해도 변경값 유지
    - `SET PERSIST`시 글로벌 변수 대상으로 변경(세션 변수는 변경 X)
    - **하지만 세션을 끊고 다시 mysql 접속하면 새로운 커넥션이 생성되는 것이므로 그 때는 세션변수도 변경된 값으로 설정되어있음**
  - Both 타입인 경우 글로벌 변수값을 SET으로 변경 > 세션 변수에는 영향 X
- 정적 변수: 변경 불가능
  - 설정파일로 변경하고 재시작해야 변경됨

```json
{
  "Version": 2,
  "mysql_dynamic_variables": {
    "join_buffer_size": {
      "Value": "524288",
      "Metadata": {
        "Host": "localhost",
        "User": "root",
        "Timestamp": 1680845134080252
      }
    }
  },
  "mysql_dynamic_parse_early_variables": {
    "max_connections": {
      "Value": "5000",
      "Metadata": {
        "Host": "localhost",
        "User": "root",
        "Timestamp": 1680844968205420
      }
    }
  }
}
```
SET PERSIST시 별도 설정파일에 기록되는데  
mac os location은 `/usr/local/mysql/data/mysqld-auto.cnf`