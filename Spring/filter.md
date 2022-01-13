# Filter

## Custom Filter 구현하기

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