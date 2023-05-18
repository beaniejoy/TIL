# Filter

## :pushpin: OncePerRequestFilter
요청(request)당 한 번 filter 동작  
Filter, GenericFilter는 여러 번 동작 가능(?)

<br>

## :pushpin: Custom Filter 구현하기

- `Filter` 인터페이스 구현, `GenericFilterBean` 상속받아서 Filter를 만드는 방법 존재
- `GenericFilterBean`은 doFilter() 하나만 Overriding해도 된다.
```kt
class JwtAuthenticationFilter(
    private val jwtTokenUtil: JwtTokenUtil
) : GenericFilterBean() {

    //...

    @Throws(IOException::class, ServletException::class)
    override fun doFilter(
        request: ServletRequest, 
        response: ServletResponse, 
        chain: FilterChain) {

        //...

    }
}
```
- `Filter`는 `init()`, `doFilter()`, `destroy()` 세가지를 모두 구현해야 한다.

<br>

## :pushpin: Caching Request, Response body

본래 request, response body 내용은 핸들러에서 body를 받기 전에 읽으면 실제 컨트롤러에서 못 읽게 된다.
`ContentCachingRequestWrapper`, `ContentCachingResponseWrapper` 활용
```kotlin
@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
class RequestResponseLoggingFilter: OncePerRequestFilter() {
    private val log = KotlinLogging.logger {}

    companion object {
        const val REQUEST_ID = "request_id"
    }

    override fun doFilterInternal(
        request: HttpServletRequest,
        response: HttpServletResponse,
        filterChain: FilterChain
    ) {
        val cachingRequestWrapper = ContentCachingRequestWrapper(request)
        val cachingResponseWrapper = ContentCachingResponseWrapper(response)

        //...

        filterChain.doFilter(request, response)

        //...
        cachingResponseWrapper.copyBodyToResponse()
    }
}
```
