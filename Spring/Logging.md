# Logging 적용해보기

- 스프링 부트의 기본으로 설정되어 있어서 사용시 별도로 라이브러리를 추가하지 않아도 된다.
- Spring Boot starter web에는 기본적으로 logback이 slf4j 구현체로 등록되어 있다.  
  - spring-boot-starter-web 안에 spring-boot-starter-logging에 구현체가 있음
- `Logger`, `Appender`, `Encoder` 세 가지 설정 요소 기억

<br>

## :pushpin: logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <include resource="org/springframework/boot/logging/logback/console-appender.xml"/>
</configuration>
```
- 기존 spring boot에서 등록하고 있는 설정 내용들을 include해서 가져올 수 있다.

<br>

## :pushpin: MDC 이용한 logback pattern 적용

- `MDC(Mapped Diagnostic Context)`
- 멀티 쓰레드 환경에서 현재 실행중인 쓰레드에 대한 메타정보를 따로 관리할 수 있는 저장소
- ThreadLocal과 관련 있음
  - ThreadLocal은 쓰레드별로 값을 저장, 읽을 수 있는 저장소(Map 형태)
  - 만약 쓰레드 풀을 사용해서 쓰레드 관리한다면 ThreadLocal에 저장하고 쓰레드를 풀에 반납하기 전에 clear 해야 한다.
  - logback의 MDC가 내부적으로 ThreadLocal 사용
  - **즉, MDC도 사용하고 쓰레드가 종료되기 전에 명시적으로 clear 해야 한다.**

```kotlin
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
class LoggingFilter: OncePerRequestFilter() {
    private val log = KotlinLogging.logger {}

    companion object {
        const val REQUEST_ID = "request_id"
    }

    override fun doFilterInternal(
        request: HttpServletRequest,
        response: HttpServletResponse,
        filterChain: FilterChain,
    ) {
        // TODO: request id 값 어떤 걸로 할 것인지
        val requestId = UUID.randomUUID().toString().substring(0, 8)
        MDC.put(REQUEST_ID, requestId)

        log.info{ "$REQUEST_ID = $requestId" }
        filterChain.doFilter(request, response)

        MDC.remove(REQUEST_ID)
    }
}
```
```xml
<!-- Pattern -->
<property name="LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %clr(%5level) [%15.15t] [%X{request_id}] %clr(%-40.40logger{39}){cyan} : %m%n%wEx"/>
```
MDC 등록 후 `logback-spring.xml` 설정파일에서 pattern 등록할 때 MDC 저장된 내용 사용 가능

<br>

## :pushpin: Filter 순서 조정
- Spring Security와 같이 사용한다면 filter 순서 조정이 필요하다.
- request시 할당받은 id를 쓰레드별로 지정해야 하는데 spring security custom filter에서는 이를 가져오지 못한다.
- spring security filter는 기본적으로 order 우선순위 값이 `-100`이라서 가장 먼저 수행된다.

```yaml
spring:
  security:
    filter:
      order: 10
```
- security filter의 order 값을 10으로 주고 LoggingFilter의 order를 1로 주면 해결은 가능
