# RestTemplate

<br>

## 제너릭 타입으로 응답객체 받기

```java
RestTemplate restTemplate = new RestTemplate();
KakaoPayReady kakaoPayReady = restTemplate.postForObject(uri, body, KakaoPayReady.class);
```
- 이런 방식으로 사용자 정의의 클래스 형태로 응답객체를 바로 받아 볼 수 있다.
- RestTemplate을 통해 요청 보낼 때 클래스 정보도 같이 보내면 된다.
- 그런데 여기서 응답 객체로 제너릭 타입을 받으려 한다면 에러가 발생한다.

```java
ResponseWrapper<UserInfoResponse> userInfoResponseWrapper = 
    restTemplate.postForObject(uri, body, ResponseWrapper<UserInfoResponse>.class)
```
- 이렇게 작성하면 안된다.
- 이 때 `ParameterizedTypeReference` 를 사용

```java
ResponseWrapper<UserInfoResponse> userInfoResponseWrapper = 
    restTemplate.postForObject(uri, body, new ParameterizedTypeReference<ResponseWrapper<UserInfoResponse>>() {})
```
- 이렇게 `ParameterizedTypeReference` 로 감싸서 보내야 한다.

<br>

## 코틀린에서 제네릭 타입 넘기기
```kt
val userInfoResponseWrapper: ResponseWrapper<UserInfoResponse> = 
    restTemplate.exchange(
        uri, 
        HttpMethod.GET, 
        null, 
        object : ParameterizedTypeReference<ResponseWrapper<UserInfoResponse>>() {})
```
- 코틀린에서 익명 클래스를 인자로 넘길 때 `object : 익명클래스 구현` 이렇게 앞에 `object`를 붙여야 한다.

<br>

## 에러 메시지 한글 깨짐 현상

```kt
try {
    restTemplate.put(papiUri + uri, requestBody)
} catch (e: HttpStatusCodeException) {
    handleException(e)
}
```
- `e.responseBodyAsString` 에서 한글로 들어온 에러메시지가 깨지는 현상 발생
- 기본적으로 `ISO_8859_1`로 인코딩되기 때문
- `UTF-8`로 변경해야할 필요 있음

```kt
val errorMessage = String(e.responseBodyAsByteArray, StandardCharsets.UTF_8)
```
- byteArray로 받아서 UTF-8로 인코딩