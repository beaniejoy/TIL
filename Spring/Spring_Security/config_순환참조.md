# Config Circular Reference Issue

- `@Component`로 등록된 bean 중에서 주의해야할 점 존재
- 생성자를 통해 bean 주입 받는 객체 중에 config 파일에서 bean 등록한 것을 주입받으려할 때 주의
- 초기 실행시 config에서 bean 등록한 내용을 가져오기 위해 config 파일을 참조하게 된다.
- 여기서 발생할 수 있는 순환참조 에러에 대해서 정리

<br>

## 상황
- Custom `AbstractAuthenticationProcessingFilter` 상속받은 클래스를 security config에 적용하고자 함
- 해당 Filter에 같이 적용할 `AuthenticationProvider` custom 구현체도 같이 config에 적용하고자 함
- `UserDetailsService` -> `UserDetailsServiceImpl`로 구현체를 적용한 상황

<br>

## 초기 설정 상황
```kotlin
@Configuration
@EnableWebSecurity
class SecurityConfig {
    @Autowired
    lateinit var authenticationConfiguration: AuthenticationConfiguration

    // @Component로 bean 등록한 상황
    @Autowired
    lateinit var apiAuthenticationProvider: ApiAuthenticationProvider

    //...

    @Bean
    fun passwordEncoder(): PasswordEncoder {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder()
    }

    //...

    @Bean
    fun authenticationManager(): AuthenticationManager {
        val authenticationManager = authenticationConfiguration.authenticationManager as ProviderManager
        authenticationManager.providers.add(apiAuthenticationProvider)
        return authenticationManager
    }

    //...
}
```
- custom filter에 적용할 authenticationManager bean 등록할 때 provider도 삽입

```kotlin
@Component
class ApiAuthenticationProvider(
    private val userDetailsService: UserDetailsService,
    private val passwordEncoder: PasswordEncoder
) : AuthenticationProvider {
```
- `ApiAuthenticationProvider`에서 passwordEncoder를 주입받고 있음
- 그렇게 되면 `passwordEncoder` bean 설정한 `SecurityConfig` 파일을 참조하게 됨
- `SecurityConfig`는 `apiAuthenticationProvider`를 다시 참조하고 있음
- 이렇게 되면 다음과 같은 에러 발생
```
┌─────┐
|  securityConfig (field public io.beaniejoy.dongnecafe.common.security.ApiAuthenticationProvider io.beaniejoy.dongnecafe.common.config.SecurityConfig.apiAuthenticationProvider)
↑     ↓
|  apiAuthenticationProvider defined in file [/Users/beanie.joy/Dev/project/dongne-cafe-api/dongne-account-api/build/classes/kotlin/main/io/beaniejoy/dongnecafe/common/security/ApiAuthenticationProvider.class]
└─────┘
```
- 순환 참조(circular reference) 에러 발생
- 순환 고리를 끊어줘야 한다.

<br>

## 해결책
- 해결책은 여러가지가 존재
- 먼저 config 파일을 분리해서 passwordEncoder bean 등록하는 것을 따로 분리시키는 것
- 다음은 passwordEncoder를 주입받고 있는 custom provider를 `@Component` 설정 제거
  - custom provider를 config에서 bean 등록하는 것으로 변경
- 두 번째 방법을 선택

```kotlin
// SecurityConfig.kt
@Autowired
lateinit var userDetailsService: UserDetailsService

//...

@Bean
fun apiAuthenticationProvider(): AuthenticationProvider {
    return ApiAuthenticationProvider(
        userDetailsService = userDetailsService,
        passwordEncoder = passwordEncoder()
    )
}

//...

@Bean
fun authenticationManager(): AuthenticationManager {
    val authenticationManager = authenticationConfiguration.authenticationManager as ProviderManager
    authenticationManager.providers.add(apiAuthenticationProvider())
    return authenticationManager
}
```