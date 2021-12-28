# spring-boot-starter module

## web

```gradle
implementation 'org.springframework.boot:spring-boot-starter-web'
```
- Spring Data web support by default, 기본적은 web 관련 설정들을 알아서 해준다.
- `@EnableWebMvc`, `@EnableSpringDataWebSupport` 어노테이션을 따로 명시하지 않아도 starter에서 알아서 등록해준다.