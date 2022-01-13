# 함수 정의 및 호출

- 쉬운 함수 호출
- 확장 함수와 확장 프로퍼티

<br>

## 📌 쉬운 함수 호출

### 이름 붙인 인자
p106

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}
```
- Java는 함수 호출을 무조건 순차적으로 인자를 적용해야 함
- 파라미터 개수가 많은 메소드를 호출하기 위해 모든 인자를 넣어야 되는 상황에서 파라미터 순서를 계속 확인해야한다. (가독성에 문제가 있음)
- 코틀린은 인자에 파라미터 이름을 붙일 수 있다. (가독성이 좋음)
- 파라미터 이름을 지정해서 인자를 넣으면 정의된 함수 파라미터 순서대로 작성을 안해도 된다. (순서 무시)
```kt
joinToString(
  listOf(1, 2, 3), 
  prefix = "(", 
  postfix = ")", 
  separator = ",")
```

### Default 파라미터 값

- 자바에서는 일부 클래스에서 Overloading한 메소드가 너무 많아진다는 문제가 있음
```java
public String hello(String param1) {...}
public String hello(String param1, String param2) {...}
public String hello(String param1, String param2, String param3) {...}
```
- 코틀린에서는 이러한 오버로딩 문제를 디폴트 파라미터 값으로 피할 수 있음
```kt
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
  //...
}

>> joinToString(list, ", ", "(", ")")
>> joinToString(list, ", ") // prefix, postfix 생략(default 값으로 설정)
>> joinToString(list, postfix = ";", prefix = "# ")
```
- 일반 호출 문법(파라미터 이름 지정 없이 쭉 나열)을 사용하면 정의된 파라미터 순서대로 인자를 넣어야 한다.
- 일부 생략하면 뒷부분의 인자들이 생략된다. (디폴트 값이 있어야 함)
- 코틀린에서는 `2^(n-1)`개의 오버로딩 메소드가 생길 것이다.  
  (n은 default 값 설정된 파라미터 개수)

#### @JvmOverloads
- 자바에서 코틀린 함수를 호출하는 경우 자바는 디폴트 파라미터 값이란 개념이 없음
- 코틀린 함수에서 디폴트 값을 지정해도 자바에서는 함수의 모든 인자를 명시해야 함
- 자바 쪽에서도 코틀린에서 지정한 디폴트 파라미터 값을 이용하고 싶으면 `@JvmOverloads`사용하면 됨
```kt
@JvmOverloads
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postfix: String = ""
): String {
  //...
}
```
- 자바 코드로 변환하면 디폴트 값이 있는 파라미터들 기준으로 맨 오른쪽 인자부터 해서 하나씩 줄인 오버로딩 메소드를 만들어 낸다.
```java
String jointToString(Collection<T> collection, String separator, String prefix, String postfix);

String jointToString(Collection<T> collection, String separator, String prefix);

String jointToString(Collection<T> collection, String separator);

String jointToString(Collection<T> collection);
```

### 최상위 함수, 프로퍼티
#### 최상위 함수
- 자바에서는 Util성 정적 메소드를 담아두는 특별한 상태나 인스턴스 메소드는 없는 **Util 클래스**가 생겨난다. (`Collections` 가 대표적인 예)
- 코틀린은 `최상위 함수`를 제공한다.
```kt
package strings
fun joinToString(...): String {...}
```
```java
package strings;
// kotlin 파일이 join.kt 인경우
public class JoinKt {
  public static String joinToString(...) {...}
}
```

#### @JvmName
- 위에서 `JoinKt.java` 처럼 코틀린 파일 이름을 기준으로 자동으로 자바 파일 이름을 생성
- 최상위 함수가 포함되는 클래스 이름을 변경하고 싶으면 `@JvmName`을 사용
```kt
// package 이름 선언 앞, 파일의 맨 앞에 명시해야 함
@file:JvmName("StringFunctions")
package strings
fun joinToString(...): String {...}
...
```
```java
StringFunctions.joinToString(...);
```

#### 최상위 프로퍼티
```kt
val UNIX_LINE_SEPARATOR = "\n"
// private static final String UNIX_LINE_SEPARATOR = "\n";

const val UNIX_LINE_SEPARATOR = "\n"
// public static final String UNIX_LINE_SEPARATOR = "\n";
```
- `const` 안 붙이면 자바에서 getter로 해당 최상위 프로퍼티를 받아야 한다.
- `const`가 자바에서 사용되는 상수 표현과 유사하다.

<br>

## 📌 확장 함수와 확장 프로퍼티

- 기존 Java API의 코드를 수정하지 않고(외부 모듈에 대해서는 수정도 못하지만..) 코틀린이 제공하는 여러 편리한 기능을 사용할 수 있음
- 확장 함수는 어떤 클래스의 맴버 메소드인 것처럼 호출 가능  
  (실제로는 해당 클래스의 밖에서 선언된 함수)
- 수신 객체 타입(receiver type), 수신 객체(receiver object) 개념 등장
```kt
fun String.lastChar(): Char = this.get(this.length - 1)
```
- `String`: 수신 객체 타입
- `this`: 수신 객체
  - `this`는 확장 함수 본문에서는 **생략할 수 있다.**
- 확장 함수 본문 안에서는 수신 객체 타입의 클래스 내부에서만 사용할 수 있는 private, protected 맴버에 대해서 접근할 수 없음(`캡슐화`)

### 확장함수의 import, java 호출
- 확장 함수를 사용하기 위해 그 함수를 다른 클래스나 함수와 마찬가지로 import 해야함
```kt
// import strings.* 도 가능
import strings.lastChar
val c = "Kotlin".lastChar()

// 이런 식으로 확장함수 alias 기능도 있음
import strings.lastChar as last
val c = "Kotlin".last()
```
- 내부적으로 확장함수는 수신 객체를 첫 번째 인자로 받는 정적 메소드
  - 확장 함수를 호출해도 다른 어댑터 객체나 실행 시점 부가 비용이 전혀 발생하지 않음(단지 정적 메소드일 뿐)
```java
char c = StringUtilKt.lastChar("Java");
```

### 확장함수는 Override 할 수 없다.
```kt
open class View {
  open fun click() = println("View clicked")
}

class Button: View() {
  override fun click() = println("Button clicked")
}

>> val vie: View = Button()
>> view.click()
// Button clicked
```
- java와 같이 override 된 메소드를 호출하게 됨
```kt
fun View.showOff() {
    println("View ShowOff")
    this.click()
}

fun Button.showOff() {
    println("Button ShowOff")
    this.click()
}

>> val view: View = Button()
>> view.showOff()
// View ShowOff (정적 타입의 확장함수 호출)
// Button clicked (동적 타입의 override 메소드 호출)
```
- 확장함수는 클래스 일부가 아니라 클래스 밖에서 선언되는 것
- 수신 객체로 지정한 변수의 정적 타입에 의해 어떤 확장 함수가 호출될지 결정
- 확장함수 내부에서 수신객체(this)에 대한 메소드 호출시 **동적인 타입(Button)의 override한 메소드가 호출된다.**
- 또한 확장함수와 수신객체타입의 메소드 이름과 시그니처가 같은 상황에서는 확장함수가 아닌 멤버함수가 호출된다. 그렇기에 확장함수 정의할 때 신중해야 한다.  
(멤버함수의 우선순위가 더 높다.)

### 확장 프로퍼티

- 실제로 확장 프로퍼티는 아무 상태도 가질 수 없음
- 책에서는 `뒷받침하는 필드`(저장하기 위한 필드)가 없다는 표현도 있음
```kt
val String.lastChar: Char
  get() = get(length - 1)
```
- 일반적인 프로퍼티와 비슷하고 수신 객체 클래스가 추가된 것일 뿐 (`this.get(length - 1)`)
- 확장 프로퍼티는 최소한 getter는 정의해야 한다.

```kt
var StringBuilder.lastChar: Char
  get() = get(length - 1)
  set(value: Char) {
      setCharAt(length - 1, value)
  }
```
- `var`로 만들어서 setter 지정도 가능(수신 객체의 변경)
- 자바에서 활용하려면 
  - `ExtendsStringKt.getLastChar(new StringBuilder("Java!"));`

<br>

## 📌 컬렉션 처리
- vararg
- infix 함수 (중위 함수)
- 구조 분해 선언

### vararg
```kt
val list = listOf(1, 2, 3, 4, 5)

fun listOf<T>(vararg values: T): List<T> {...}
```
- kotlin에서 지원하는 `listOf` 함수의 인자는 `vararg`다.
- 자바에서 `...` 였다면 코틀린에서는 `vararg`를 명시적으로 파라미터 앞에 붙여야 함

```java
public void method(String... strArr) {...}

String[] strArr = new String[10];
XXX.method(strArr); // 바로 넘길 수 있음
```
```kt
fun method(vararg strArr: String) {...}

fun main(args: Array<String>) {
  method(*args)
}
```
- 자바에서는 배열을 그냥 넘겨도 되지만 코틀린에서는 배열을 명시적으로 풀어서 배열의 각 원소가 인자로 전달되게 해야 함
- 코틀린에서 스프레드 연산자(`*`)가 해당 역할을 수행