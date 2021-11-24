# RestTemplate

## 제너릭 타입으로 응답객체 받기

```java
RestTemplate restTemplate = new RestTemplate();
KakaoPayReady kakaoPayReady = restTemplate.postForObject(uri, body, KakaoPayReady.class);
```
- 이런 방식으로 사용자 정의의 클래스 형태로 응답객체를 바로 받아 볼 수 있다.
- RestTemplate을 통해 요청응ㄹ 보낼 때 클래스 정보도 같이 보내면 된다.
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