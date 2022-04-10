# Kotlin과 Java의 Class 형태


```kotlin
class Member(
  private val name: String,
  private val age: Int
)
```
- default value가 프로퍼티에 하나라도 없으면 default constructor가 생성되지 않는다.

```java
public class Member {
  private final String name;
  private final Integer age;

  public Member(String name, Integer age) {
    this.name = name;
    this.age = age;
  } 
}
```

```kt
data class Member(
  private var name: String,
  var age: Int
) {
  fun getName(): String {
    return "customize ${this.name}"
  }

  fun setName(name: String) {
    this.name = name
  }
}
```
- custom getter 설정시 주생성자 인자에 private 접근자 설정
(그래야 주생성자에 의한 getter가 생성되지 않음)
- 대신 setter도 생성되지 않기 때문에 setter가 필요한 경우 따로 setter를 등록해야 한다.