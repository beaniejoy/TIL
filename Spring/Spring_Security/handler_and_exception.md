# Handler (Success, Failure)

- SuccessHandler
- FailureHandler
- Spring Security는 SuccessHandler, FailureHandler를 지원

<br>

## AuthenticationFailureHandler
- 기본적으로 `SimpleUrlAuthenticationFailureHandler` 사용됨

### SimpleUrlAuthenticationFailureHandler
- defaultUrl을 설정하지 않으면 `response.sendError`하게 됨
- response.sendError 하게 되면 Tomcat에 error 호출
  - Tomcat에 error를 보내어 default로 설정된 `/error`를 호출
  - `/error` 호출에 대한 default error page를 view 호출
  - 만약 `/error` 경로에 대한 security config 예외처리가 따로 되어있지 않으면 `Forbidden` 에러로 원치않은 에러 반환 값을 받게 됨


<br>

## ExceptionTranslationFilter
- 인증, 인가 예외 처리 필터
- 바로 뒤의 필터인 `FilterSecurityInterceptor`에서 발생한 두 종류의 Exception 처리 용도 필터
  - `AuthenticationException`, `AccessDeniedException`
