# TimeZone 이슈

- MySQL 5.7.x 버전
- Spring Boot

## 문제상황 및 해결

- MySQL - Spring Boot Application 연결 때 time_zone 불일치 이슈가 있었음
- `spring.datasource.url` 에 `&serverTimezone=Asia/Seoul` 해도 DB 데이터와 JPA로 가져왔을 때 데이터간 시간차이가 발생(9시간)
- JVM 전역으로 TimeZone을 설정하는 것이 중요

```java
@PostConstruct
public void started() {
    TimeZone.setDefault(TimeZone.getTimeZone("Asia/Seoul"));
}
```
- main application 클래스에 TimeZone을 `Asia/Seoul`로 설정함으로써 DB timezone과 일치시키면 된다.