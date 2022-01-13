# Jackson 관련 내용

- `spring-boot-starter-web` 모듈에서는 jackson bind를 제공해준다.

## Spring에서의 jackson
- Spring에서 Jackson 라이브러리가 있으면 Object와 JSON간에 변환을 자동으로 해준다.
- Jackson은 기본적으로 프로퍼티로 작동한다. (getter, setter)

### Getter 프로퍼티

#### Object -> JSON 관점 (serialize)
```java
public class ExampleGetter {

  //...

  public Long getHelloId() {
      return id;
  }

  public String getHelloName() {
      return name;
  }

  public String getHelloEmail() {
      return email;
  }

  public String getHelloAddress() {
      return address;
  }
}
```
```json
{
  "helloId": 1,
  "helloName": "...",
  "helloEmail": "...",
  "helloAddress": "..."
}
```
- 맴버변수 기준이 아닌 getter의 name 기준으로 JSON 변환을 한다.
- getter의 get 뒷부분이 JSON key name 기준이 된다.
- `getHelloName`, `gethelloName` 둘다 JSON에는 `helloName`으로 표시
  - get 다음에 대소문자 상관없다는 의미
  - but 네이밍 규칙에 따라 get 다음에 대문자로 구분

```java
public Long getHello_id() {
    return id;
}

public String getHello_name() {
    return name;
}
```
- 만약 JSON에는 snake case로 보이고 싶으면 위와 같이 getter 이름을 만들면 됨
- but 이러한 방식은 관례적으로 사용하지 않음 (이렇게 사용하는 걸 본적이 없음...)
- 그럼 getter에 지정한 name을 snake case로 변환하고 싶으면 아래와 같이 어노테이션 추가하면 된다.

```java
@JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)
public class ExampleGetter {...}
```
- `@JsonNaming` 을 통해 Snake Case 설정하면 된다.
```json
{
  "hello_id": 1,
  "hello_name": "beanie",
  "hello_email": "beanie@example.com",
  "hello_address": "address1"
}
```


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


## Reference
- [[Spring] Jackson 라이브러리 이해하기](https://mommoo.tistory.com/83)
