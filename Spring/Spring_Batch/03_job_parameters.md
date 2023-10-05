# JobParameter 사용 방법

- `jobParameters` 에서 직접 추출
```java
String value = jobParameters.getString("chunkSize", "10");
int chunkSize = StringUtils.isNotEmpty(value) ? Integer.parseInt(value) : 10;
```
spring batch 실행시 `-chunkSize=20` parameter 적용해야 한다.

- Spring Expression Language(SpEL) 사용하는 방식

```java
@Bean
@JobScope
public Step chunkBaseStep(@Value("#{jobParameters[chunkSize]}") String value) {
  //...
}
```
`@JobScope` 설정 후 `@Value("#{jobParameters[chunkSize]}")`로 외부에서 받아들임
