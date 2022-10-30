# User (Spring Security)

<br>

## :pushpin: 구현체
- `UserDetails` 인터페이스를 구현
- `username`, `password`, `authorities`에 대한 getter 존재

<br>

## :pushpin: password 관련 내용
- 보통 `UsernamePasswordAuthenticationFilter` -> `AuthenticationManager` authenticate 과정 진행
- `AuthenticationManager` -> `ProviderManager` 구현체에게 실제 알맞는 인증 프로세스를 수행할 authencationProvider에게 인증을 위임
- `AuthenticationProvider` 구현체에서 `UserDetailsService` `loadUserByUsername`를 수행
  - `UserDetailsService` 구현체에서 username(or email)기준으로 Member Entity를 조회
  - 여기서 `UserDetails` 타입으로 반환받음
  - 여기 안에는 password 필드에 사용자가 회원가입시 설정해두었던 password 값이 들어있음
- `PasswordEncoder`로 input받은 pw와 저장된 pw를 matching(인증 절차 과정)
- `ProviderManager`는 인증 절차를 마무리한 authentication 객체를 받아서 erase password 작업을 진행
```java
if (result != null) {
  if (this.eraseCredentialsAfterAuthentication && (result instanceof CredentialsContainer)) {
    // Authentication is complete. Remove credentials and other secret data
    // from authentication
    ((CredentialsContainer) result).eraseCredentials();
  }
  // If the parent AuthenticationManager was attempted and successful then it
  // will publish an AuthenticationSuccessEvent
  // This check prevents a duplicate AuthenticationSuccessEvent if the parent
  // AuthenticationManager already published it
  if (parentResult == null) {
    this.eventPublisher.publishAuthenticationSuccess(result);
  }

  return result;
}
```
- `eraseCredentials`를 통해 password null 처리
- 그래서 그 이후 SuccessHandler 같은 곳에서 authentication principal 받아서 password 찍어보면 null로 나옴