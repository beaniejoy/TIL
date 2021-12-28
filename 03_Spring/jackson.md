# Jackson 관련 내용

- `spring-boot-starter-web` 모듈에서는 jackson bind를 제공해준다.

## Annotation

### @JsonProperty

- 본래 Jackson은 기본적으로 JSON - Object binding을 할 때프로퍼티(getter, setter 메소드)로 움직인다.
- 필드 기준으로 변경하고 싶으면 `@JsonProperty` 사용하면 됨
- input, output 에 대해서 `@JsonProperty`에 지정한 name으로 매핑을 해준다.

```java
public class MemberDto {
    @JsonProperty("hello_id")
    private Long id;

    @JsonProperty("hello_name")
    private String name;

    @JsonProperty("hello_email")
    private String email;

    @JsonProperty("hello_address")
    private String address;
}
```
- 여기서 필드에 어노테이션 설정했다면 getter가 따로 없어도 JSON binding이 잘 이루어진다.
- `@RequestBody`로 JSON을 받을 때에도 적용된다.

### @JsonIgnoreProperties

```java
@JsonIgnoreProperties(ignoreUnknown = true)
public class Member {
  //...
}
```
- deserialization(JSON -> Object) 과정에서 인식되지 않는 필드들에 대해서 에러가 아닌 무시를 한다.
- default는 `false`로 규격에 맞지 않는(필드 이름에 맞지 않는) JSON 데이터가 들어오면 에러 발생
  - `UnrecongnizedPropertyException`

## References
- https://mommoo.tistory.com/83