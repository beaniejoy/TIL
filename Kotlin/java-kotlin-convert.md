# Kotlin과 Java의 Class 변환

kotlin - java 간 호환 이슈 기록

<br>

## data class 

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

> 그런데 위의 내용대로 data class에 custom getter를 사용하지 말자  
> 그리고 data class를 overriding해서 쓰거나 extends해서 사용하지 말자

<br>

## Java interface default method

```kt
interface KotlinInterface {
  fun hello(): String {
    return "hello"
  }
}
```

```java
interface JavaInterface {
  default String hello() {
    return "hello"
  }
}
```

kotlin에서 java의 interface default method를 사용하려고 method body를 구현했다면  
다르게 Java로 변환된다.  
(default method가 아닌 public static final class인 DefaultImpls 클래스 내부에서 static method로 제공하는 것으로 변환된다.)  

kotlin은 그자체로 java의 interface default method를 변환할 수 없다. (java 8부터 생긴 기능)  

```
-Xjvm-default=all-compatibility
```
kotlin compiler 옵션에 위의 내용을 추가해야 한다.

```kts
tasks.withType<KotlinCompile> {
    kotlinOptions {
        freeCompilerArgs += arrayOf(
            "-Xjsr305=strict",
            "-Xjvm-default=all-compatibility" // @JvmDefault > java interface default method
        )
        jvmTarget = JavaVersion.current().toString()
    }
}
```
gradle 설정에서 KotlinCompile task 설정에 argument를 추가해준다.  
그리고 Intellij로 애플리케이션 실행시 설정할 것이 있는데  
```
IntelliJ Settings > Build, Execution, Deployment > Build Tools > Gradle 
```
위의 메뉴로 들어가서 `Build and run using`을 Gradle로 변경해준다.

> -Xjvm-default의 옵션이 all, all-compatibility가 있는데 어떤 차이가 있는지는 모르겠다.