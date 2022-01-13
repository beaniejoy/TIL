# Design Pattern

- 프로그램을 작성하다보면 비슷한 상황에 직면하게 되는 경우가 많이 존재
- 이러한 상황에서 이전의 많은 개발자들이 고민하고 정제한 일종의 표준 설계 패턴

## 종류

- 어댑터 패턴(Adapter Pattern)
- 프록시 패턴(Proxy Pattern)
- 데코레이터 패턴(Decorator Pattern)
- 싱글턴 패턴(Singleton Pattern)

## Adapter Pattern

- ODBC/JDBC 같이 다양한 DB들을 공통의 인터페이스로 조작할 수 있게 해줌(어댑터 패턴 적용)
- 플랫폼별(OS별) JRE도 일종의 어댑터 패턴 적용된 사례

```java
public class BusinessA {

  public void executeBusinessA() {
    System.out.println("execute business A");
  }
}

public class BusinessB {

  public void executeBusinessB() {
    System.out.println("execute business B");
  }
}

public class ClientCompany {

  public static void main(String[] args) {
    BusinessA bsA = new BusinessA();
    BusinessA bsB = new BusinessB();

    bsA.executeBusinessA();
    bsB.executeBusinessB();
  }
}
```
- client에서는 비슷하게 작동하는 메소드에 대해서 다른 메서드명으로 작동
- `BusinessA`, `BusinessB` - `ClientCompany` 중간에 어댑터를 두어 통일된 방식으로 메서드를 제공 가능

```java
public interface AdapterBusiness {
  void executeBusiness();
}

public class AdapterBusinessA implements AdapterBusiness {
  private BusinessA businessA;

  @Override
  public void executeBusiness() {
    // 어댑터의 메서드 안에서 진짜 비즈니스 로직이 담긴 메서드를 실행
    businessA.executeBusinessA();
  }
}

public class AdapterBusinessB implements AdapterBusiness {
  private BusinessB businessB;

  @Override
  public void executeBusiness() {
    businessB.executeBusinessB();
  }
}

public class ClientCompany {

  public static void main(String[] args) {
    AdapterBusiness bsA = new AdapterBusinessA();
    AdapterBusiness bsB = new AdapterBusinessB();

    // AdapterBusiness 인터페이스를 통해 통일된 메서드 명으로 제공
    bsA.executeBusiness();
    bsB.executeBusiness();
  }
}
```
- 어댑터 패턴은 실제 로직이 담긴 메서드의 객체를 어탭터 클래스 필드로 담아두는 형태
- 어탭터 메서드 안에 필드 객체의 실제 메서드를 실행하는 방식

## Proxy Pattern

- 실제 서비스 객체가 가진 메서드와 같은 이름의 메서드 사용
- 인터페이스를 통해 Proxy 객체와 실제 객체 두 개를 만든다.

```java
public interface Service {
  String runService();
}

public class ServiceImpl implements Service {

  @Override
  public String runService() {
    return "서비스 실행 완료";
  }
}

public class Proxy implements Service {
  private Service service;

  @Override
  public String runService() {
    service = new ServiceImpl();
    return service.runService();
  }
}
```
- 어댑터 패턴과 다른 점은 어댑터 패턴은 어탭터 클래스 안에 실제 메소드를 담은 객체 필드가 존재하지만 어댑터 클래스와 같은 인터페이스를 구현한 구현체는 아니다. (별개)
- 프록시는 실제 서비스에 대한 참조 변수를 갖는다.
- 프록시와 실제 서비스는 같은 인터페이스를 구현한다.

## Decorator Pattern
```java
public interface Service {
  String runService();
}

public class ServiceImpl implements Service {

  @Override
  public String runService() {
    return "서비스 실행 완료";
  }
}

// 형태는 Proxy와 비슷해보임
public class Decorator implements Service {
  private Service service;

  @Override
  public String runService() {
    // 여기서 return 할 내용에 대해 장식을 덧입힌다.
    service = new ServiceImpl();
    return "decorator 추가: " + service.runService();
  }
}
```
- 프록시 패턴과 형태는 유사해 보인다.
- **프록시 패턴**
  - 실제 서비스 메서드의 return을 그대로 내보낸다.(특별한 경우가 아니면 변경하지 않음)
  - 제어의 흐름을 변경, 별도의 로직 처리를 목적으로 한다.
- **데코레이터 패턴**
  - 실제 서비스 메서드의 return에 일종의 데코레이터를 입힌다.(추가, 변경)

## Singleton Pattern
```java
public class Singleton {
  private static Singleton singletonObj;

  private Singleton() {}

  public static Singleton getInstance() {
    if(singletonObj == null) {
      singletonObj = new Singleton();
    }

    return singletonObj;
  }
}
```
- 인스턴스 하나만 만들어 사용하기 위한 패턴


# 디자인패턴 관련 참고 블로그

- [Decorator Pattern](https://gmlwjd9405.github.io/2018/07/09/decorator-pattern.html)
- [Strategy Pattern](https://gmlwjd9405.github.io/2018/07/06/strategy-pattern.html)