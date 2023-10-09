# 이런 저런 Security Filter 관련

## Security Custom Filter 추가

```kt
.addFilterBefore(
    JwtAuthenticationFilter(jwtTokenUtil()),
    UsernamePasswordAuthenticationFilter::class.java
) // 인증을 위한 filter 등록(GenericFilterBean과 비교)
```
- `GenericFilterBean`, `UsernamePasswordAuthenticationFilter` 상속받아서 security 관련 custom filter를 만들 수 있다.
- 각각 security config에서 설정해야 하는 addFilter 부분에서 차이가 있는 걸로 봤음 

<br>

## DelegatingFilterProxy

- Spring Security의 Filter들을 처리하고 관리하는 Filter

### 생성 과정
- `AbstractSecurityWebApplicationInitializer` 클래스에서 `onStartup`으로 애플리케이션 실행 단계에서 filter 등록
- 그 중 `insertSpringSecurityFilterChain`을 주목할 만함
```java
private void insertSpringSecurityFilterChain(ServletContext servletContext) {
  String filterName = DEFAULT_FILTER_NAME;
  DelegatingFilterProxy springSecurityFilterChain = new DelegatingFilterProxy(filterName);
  String contextAttribute = getWebApplicationContextAttribute();
  if (contextAttribute != null) {
    springSecurityFilterChain.setContextAttribute(contextAttribute);
  }
  registerFilter(servletContext, true, filterName, springSecurityFilterChain);
}
```
- `DEFAULT_FILTER_NAME`: `springSecurityFilterChain`
- DelegatingFilterProxy