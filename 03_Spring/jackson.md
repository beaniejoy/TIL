# Jackson 관련 내용

- `spring-boot-starter-web` 모듈에서는 jackson bind를 제공해준다.

## Annotation

### @JsonIgnoreProperties

```java
@JsonIgnoreProperties(ignoreUnknown = true)
```
- deserialization(JSON -> Object) 과정에서 인식되지 않는 필드들에 대해서 에러가 아닌 무시를 한다.
- default는 `false`로 규격에 맞지 않는(필드 이름에 맞지 않는) JSON 데이터가 들어오면 에러 발생
  - `UnrecongnizedPropertyException`