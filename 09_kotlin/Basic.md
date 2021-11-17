# Kotlin 이해하기

## Kotlin 특징

- 코틀린은 기본적으로 정적 타입 지정 언어다.

### 타입 추론(type inference)

```kotlin
// return 타입 생략 가능
fun max(a: Int, b: Int) = if (a > b) a else b
```

- `코틀린 컴파일러`가 타입을 분석해서 프로그래머 대신 프로그램 구성 요소의 타입을 정해주는 기능
- 정적 타입 지정 언어인 코틀린이 반환 타입을 생략하거나 `var`를 사용할 수 있는 이유
- 타입 추론할 수 있도록 근거를 줘야 한다.

```kt
val answer: Int
answer = 42
```
- 초기화 식을 사용하지 않으면 컴파일러가 타입을 알 수 있게 명시적으로 지정해야 함

### 변수
- `val`: java의 `final`과 같은 변경 불가능한 참조변수
- `var`: 변경 가능한 참조, 일반 변수에 해당

### 스마트 캐스트(smart cast)


## Java -> Kotlin 내용

### if 구문

```kt
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```
- java에서는 3항 연산자로 `(a > b) ? a : b` 이런 식으로 표현
- 코틀린에서 if 구문은 그자체로 값을 반환해준다.

```kt
fun max(a: Int, b: Int): Int = if (a > b) a else b
```
- 식이 본문인 함수로도 표현 가능

