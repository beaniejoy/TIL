# DelegatingFilterProxy

- Spring Security의 Filter들을 처리하고 관리하는 Filter

## 생성 과정
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