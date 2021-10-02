# Flyway를 통한 DB Migration

- https://flywaydb.org/

## 설치
```shell
$ brew install flyway
```
- Mac OS 기준, homebrew 패키지로 설치

## Setting
- 먼저 Migration할 DB 서버가 실행이 되고 있어야 한다.

```properties
flyway.url=<DB url>
flyway.user=root
flyway.password=password
flyway.driver=<DB driver>
flyway.locations=filesystem:<migrate할 sql 파일 경로>
```
- `flyway.conf` 설정 내용
- url: `jdbc:mysql://localhost:3306/test-schema` 같이 경로 뒤에 query string 없이 설정

## 실행

```shell
$ flyway -configFiles=<conf파일 경로> migrate
```