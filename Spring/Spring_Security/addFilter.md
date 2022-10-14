# Security Custom Filter 추가

```kt
.addFilterBefore(
    JwtAuthenticationFilter(jwtTokenUtil()),
    UsernamePasswordAuthenticationFilter::class.java
) // 인증을 위한 filter 등록(GenericFilterBean과 비교)
```
- `GenericFilterBean`, `UsernamePasswordAuthenticationFilter` 상속받아서 security 관련 custom filter를 만들 수 있다.
- 각각 security config에서 설정해야 하는 addFilter 부분에서 차이가 있는 걸로 봤음 