# ExceptionTranslationFilter

- `AuthenticationException`
  - 인증 예외 처리
- `AccessDeniedException`
  - 인가 예외 처리

<br>

## AnonymousAuthentication

SecurityContext에 authentication이 null 상태이면 `AnonymousAuthenticationFilter`에서 anonymousAuthentication을 생성해서 SecurityContext에 담아준다.

여기서 의문이 `FilterSecurityInterceptor`에서 인가에 대한 설정정보들을 가지고 `AccessDecisionManager`에서 인가여부를 체크하게 되는데 여기서 인가가 불가한 authentication에 대해서 `AccessDeniedException`을 반환할 줄 알았다.

**하지만 `AnonymousAuthentication`에 대해서는 `AuthenticationException`으로 처리가 된다.**

```java
private void handleAccessDeniedException(HttpServletRequest request, HttpServletResponse response,
    FilterChain chain, AccessDeniedException exception) throws ServletException, IOException {
  Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
  boolean isAnonymous = this.authenticationTrustResolver.isAnonymous(authentication);
  if (isAnonymous || this.authenticationTrustResolver.isRememberMe(authentication)) {
    if (logger.isTraceEnabled()) {
      logger.trace(LogMessage.format("Sending %s to authentication entry point since access is denied",
          authentication), exception);
    }
    sendStartAuthentication(request, response, chain,
        new InsufficientAuthenticationException(
            this.messages.getMessage("ExceptionTranslationFilter.insufficientAuthentication",
                "Full authentication is required to access this resource")));
  }

  //...

  protected void sendStartAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain,
			AuthenticationException reason) throws ServletException, IOException {
		// SEC-112: Clear the SecurityContextHolder's Authentication, as the
		// existing Authentication is no longer considered valid
		SecurityContext context = SecurityContextHolder.createEmptyContext();
		SecurityContextHolder.setContext(context);
		this.requestCache.saveRequest(request, response);
		this.authenticationEntryPoint.commence(request, response, reason);
	}
}
```
`sendStartAuthentication`에서 `authenticationEntryPoint`으로 `authenticationException`을 만들어서 처리하도록 지시하고 있다.