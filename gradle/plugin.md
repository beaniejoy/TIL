# Plugins

```kotlin
plugins {
  // id "org.jetbrains.kotlin.plugin.spring" version "Version"
  kotlin("plugin.spring") version "Version" apply false
}

apply plugin: "kotlin-spring" // instead of "kotlin-allopen"
```

- 스프링 프레임워크와 Hibernate는 `CGLIB`(Code Generation Library)를 기본 바이트 코드 조작 라이브러리로 사용
- 코틀린에서는 기본적으로 자바 클래스로 변환시 `final class`
- 즉 코틀린 코드로 컴파일해서 실행할 때 `CGLIB` 라이브러리를 사용하지 못함
- `open`을 해줘야 하는데 그걸 `all-open` 플러그인에서 지원해줌
- [All-open compiler plugin(doc)](https://kotlinlang.org/docs/all-open-plugin.html)

<br>

## References
- [Kotlin + Spring 시작하기(1) - 프로젝트 초기 설정](https://velog.io/@eastperson/Kotlin-Spring-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B01-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%84%B1)