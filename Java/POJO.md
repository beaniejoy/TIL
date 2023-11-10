# POJO & Java Bean & Spring Bean

POJO > Java Bean > Spring Bean (넓은 범위 순)  
결국 더 좋은 방향으로 개념 정의

> Creating of these objects in java have evolved with time for better manageability, modularity, and flexibility in Java applications

즉, 모듈화, 관리의 편리성, 유연성이 더 좋아지는 방향으로 object에 대한 개념이 진화되어 옴

<br>

## POJO
```java
class Person {
  int age;
  String name;
  String character;
}
```
`any java object is a POJO` > 즉 Java의 어떤 형태든 상관없이 객체는 모두 POJO


<br>

## Java Bean

```java
class Person implements Serializable {
  private int age;
  private String name;
  private String character;

  // public getter setter...

}
```
- Java Bean 특징
  - `Serializable` 구현한 클래스의 객체이어야 함
  - private field
  - public getter, setter 존재
  - **no args constructor**가 존재해야 한다.

즉, `Serializable`을 구현했기 때문에 byte stream으로 직렬화가 가능해짐  
network나 db storage에 해당 데이터들을 전송하거나 저장할 수 있게 되고 반대로 읽을 수도 있게 된다. 즉 JVM application들 간에 통신과 HDD 영구 저장이 가능해짐

- **Java Bean의 장점**
  - Standardization: 다른 툴에서도 사용가능한 표준화된 형태를 제공
  - Easy Integration: 표준을 제공하깅에 다른 개발 환경, 프레임워크와 통합이 가능해짐
  - Automatic Behavior: 이부분을 이해 못함
  - Ready for Storage: 위에서 언급한 대로 네트워크를 통해 어디로 전송하거나, 저장공간에 영구 저장이 가능해짐

> JavaBeans can be easily instantiated using the `Class.newInstance()` method

<br>

## Spring Bean

java object whose class is configured with spring annotations like `@Component`, `@Service`, `@Repository`, or `@Controller`  
(Spring container can create and manage spring bean with these annotations)  

```java
@Component
public class Person {
    // Bean logic and properties
}
```

- 주요 특징
  - Spring Container에 의해 관리
  - creation을 관리
  - configuration: spring container가 어노테이션을 보고 spring bean 객체로 생성
  - lifecycle: spring bean 대상이 되는 객체들은 생성 ~ 소멸에 대한 lifecycle을 Spring Container에 의해 관리된다. (우리가 직접 생성하지 않아도 된다.)
  - Dependency Injection: 외부에 필요한 다른 Spring Bean 객체들을 알아서 주입해준다.

<br>

## References
- https://medium.com/@manjupanthangi33597/pojo-vs-java-bean-vs-spring-bean-3982344dda9d