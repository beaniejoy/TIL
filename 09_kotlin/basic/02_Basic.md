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

```java
public class EvalUtil {
    public int eval(Expr e) {
        if (e instanceof Num) {
            return ((Num) e).getValue();
        }
        if (e instanceof Sum) {
            return eval(((Sum) e).getRight()) + eval(((Sum) e).getLeft());
        }
        throw new IllegalArgumentException("unknown expression");
    }
}
```
- 자바는 인터페이스 상속 받은 객체 고유의 접근자를 호출할 때 타입 캐스팅을 해야 한다.

```kt
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left)
    }

    throw IllegalArgumentException("unknown expression")
}

fun main() {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
}
```
- 코틀린은 타입 캐스팅을 생략해도 된다.
- 코틀린 컴파일러가 캐스팅을 수행해준다. -> `스마트 캐스트`
- 스마트 캐스트는 `is`(java에서는 `instanceof` 격)로 변수에 든 타입을 검사한 후 컴파일러가 자동으로 타입 캐스팅을 수행해준다.
> `is`가 제대로 작동되기 위해서는
> - 검사한 후 값이 바뀔 수 없는 경우에 작동(`e is Num`에서 `e`는 `val`로 선언되어야 한다.)
> - 커스텀 접근자를 사용한 것이어도 안된다.
> - 원하는 타입으로 명시적으로 타입 캐스팅하려면 `as` 사용 (`val n = e as Num`)


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

## 중요 포인트

### `println()`

- 코틀린 표준 라이브러리는 여러 가지 표준 자바 라이브러리 함수를 간결하게 사용할 수 있게 감싼 래퍼를 제공한다.
- `println()`은 대표적인 예시 메서드
- 주의할 점
  - 자바함수와 간혹 겹칠 때가 있어서 java, kotlin 혼합 사용 때 주의해야 한다.
  - IDE 자동추천으로 import가 잘못될 수도 있다. 항상 확인하는 것이 좋다.

### 문(statement)과 식(expression)

- 문(statement)
  - 아무런 값을 만들어내지 않는다.
  - `{...}`로 묶임
  - 자바에서는 기본적으로 모든 제어 구조가 `문`으로 구성
- 식(expression)
  - 값을 만들어 낸다.
  - 다른 식의 하위 요소로 계산에 참여 가능
  - 코틀린에서 `if`, `when`, `try` 같은 구문들은 모두 식이다.

### list, map

- mutable list, map 형태로 return을 하지말아야 한다.
- parameter 받을 때도 immutable한 list, map으로 받아야 한다.
- 내부 값의 수정을 차단

### type inference 주의점

```kt
val number = 42
```
- 만약 number를 Long type으로 기대하고 위와 같이 선언하면 Int 타입으로 선언된다.
- Int 범위를 벗어나게 되면 Exception error 발생(주의)

```kt
val number = 42L
val number: Long = 42
```
- 위와 같이 Long에 대해서 명시적으로 표현하는 것이 좋다.

### 문자열 템플릿

- 문자열 템플릿에는 웬만하면 `${...}` 중괄호를 붙여서 사용하자
- 한글과 영어가 혼용되어 쓰이면 `$name네임` 이런 식으로 사용했을 때 name네임 전체가 변수로 인식되어 에러가 발생할 수 있다.

### Lombok 문제

- kotlin과 java를 혼용해서 사용할 때 혹은 kotlin으로 migration 할 때 java 코드에 Lombok이 들어가 있으면 에러가 발생한다.
- IntelliJ 같은 IDE를 이용해 Java -> Kotlin migration시 Lombok이 있으면 에러 발생
- Java class를 Kotlin class가 상속 받을 때 Java 코드에 Lombok이 있으면 역시 에러 발생
- Lombok은 사용에 있어서 주의가 필요하기 때문에 잘 안 사용하려는 추세로 전환
  - `@AllArgsConstructor` 같은 경우도 필드 변수 순서에 따라 데이터가 잘못 주입이 될 수도 있다.
  - `new Example(String b, String a)` 이런 경우 본래 a 필드 자리에 b 데이터가 잘 못 주입될 수도 있다.
- Kotlin Compiler가 먼저 작동하고 그다음 Lombok을 적용하기 때문에 코틀린으로 제대로 전환이 안된다.
- 그래서 웬만하면 Lombok보다 직접 클래스 코드 내부에 구현해서 사용하자

### 커스텀 접근자
- 있다는 것만 알아두고 사용은 자제하자
- JPA에서 Entity class에 사용하면 크나 큰 문제 발생 가능성이 있음

### Enum과 when 사용

- when 내부에 Enum을 분기로 사용시 else는 생략하는 것이 좋다.
- 컴파일 단계에서 Enum 클래스 중 빼먹은 케이스가 있을 시 걸러주기 때문
- else를 포함하면 빠진 케이스 중 분기처리가 필요한 것들에 대해서 누락할 수 있다.
