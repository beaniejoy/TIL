# URI

## URISyntaxException 숨기기

```java
try {
  Uri uri = new URI(HOST + path)
} catch (URISyntaxException e) {
  logger.error(e.getMessage());
}
```
- 불필요한 `try - catch` 구문 사용
- 내부적으로 해당 Exception을 처리함으로써 숨겨주는 URI Builder 클래스 존재

```java
URI uri = UriComponentsBuilder
              .fromUriString(HOST)
              .path("/v1/payment/ready")
              .encode()
              .build()
              .toUri();
```
- `try - catch`나 `throws`를 하지않아도 된다.
- 이걸 사용하는 클라이언트에서는 해당 예외처리를 하지 않아도된다.