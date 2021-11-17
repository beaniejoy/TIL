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

