# Handler (Success, Failure)

- Spring Security는 SuccessHandler, FailureHandler를 지원
- 기본적으로 설정된 Handler는 SimpleUrl

<br>

## AuthenticationFailureHandler
- 기본적으로 `SimpleUrlAuthenticationFailureHandler` 사용됨

### SimpleUrlAuthenticationFailureHandler
- defaultUrl을 설정하지 않으면 `response.sendError`하게 됨
- response.sendError 하게 되면 Tomcat에 error 호출
  - Tomcat에 error를 보내어 default로 설정된 `/error`를 호출
  - `/error` 호출에 대한 default error page를 view 호출
  - 만약 `/error` 경로에 대한 security config 예외처리가 따로 되어있지 않으면 `Forbidden` 에러로 원치않은 에러 반환 값을 받게 됨
