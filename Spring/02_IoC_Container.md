# IoC

- Inversion of Control (제어의 역전)
- A라는 클래스 안에 B라는 클래스를 선언 및 할당해서 사용하는 부분이 있다면 `A는 B에 의존`하고 있다라고 표현

```java
// class A는 class B에 의존하고 있다. (A depends on B)
public class A {

  // class A안에 class B 참조를 가지고 있음
  private B b;

  public A() {
    // A 객체를 생성할 때 B 객체를 직접 생성해서 할당
    this.b = new B();
  }
}
```
- 이러한 의존 객체(B)를 직접 생성하는 것이 아닌 외부에서 이미 생성된 객체를 주입받아서 사용하는 개념

```java
public class A {
  private B b;

  // class A 내부에서 B 객체를 생성하는 것이 아닌 
  // 이미 생성된 객체 B를 생성자를 통해 주입받음
  public A(B b) {
    this.b = b;
  }
}
```
- 객체 생성에 대한 제어를 사용자(개발자)가 하는 것이 아닌 제3자가 담당해서 알아서 주입
- 제3자의 역할을 Spring IoC에서 해주는 것
- **Spring IoC는 객체 생성(bean 객체)과 관리 역할 뿐만 아니라 의존성 주입(DI) 역할도 수행** 

## Spring IoC Container

- BeanFactory
  - ApplicationContext의 최상위 인터페이스
- Spring Application에 등록된 모든 bean들을 저장하고 관리하는 저장소 역할
- 빈 설정(config) 소스로부터 bean에 대한 설정 정의를 읽고 bean을 생성하고 제공해줌
- Bean
  - Spring IoC Container의 관리 대상인 객체
  - 의존성 관리
    - 의존관계에 해당하는 객체들을 IoC가 알아서 연결해주고 주입시켜줌
  - Lifecycle 인터페이스 지원
    - Spring IoC에 등록된 bean에 한정돼 bean의 생명주기에 따라 추가 작업 적용 가능
    - bean의 construct 기준으로 전과 후에 전처리, 후처리 지원 가능  
      (ex. `@PostConstruct`)
  - Scope (스코프)
    - `Singleton`: 애플리케이션 생성시 bean들을 최초 등록 후 동일한 객체를 계속 사용
    - `Prototype`: bean을 사용할 때마다 새로운 객체를 생성해서 사용
    - 객체를 생성하는 데 많은 비용이 들기 때문에 IoC에서는 기본적으로 `Singleton`으로 적용

## BeanFactory
```
BeanFactory
└── ApplicationContext
    ├── GenericXmlApplicationContext
    ├── ClassPathXmlApplicationContext
    ├── AnnotationConfigApplicationContext
    └── ...
```
- `BeanFactory`: 스프링 컨텍스트의 최상위 인터페이스
- `getBean()` 제공

### ApplicationContext
- BeanFactory를 상속
- BeanFactory의 기본 기능들에서 여러 부가기능들 제공
```
ApplicationContext
  ├──> MessageSource
  ├──> EnvironmentCapable
  ├──> ApplicationEventPublisher
  └──> ResourceLoader
```
#### EnvironmentCapable
- 프로파일과 프로퍼티를 다루는 인터페이스  
- `@Profile("profile_name")`
  - 빈들의 그룹
  - Environment 의 역할은 활성화할 프로파일 확인 및 설정
  - `-Dspring.profiles.active="a,b,c,..."`
- Property
  - `@Value("${app.name}")` 이런 식으로 프로퍼티 값을 전달할 수 있다.

#### MessageSource
- 국제화(`i18n`) 기능 제공

#### ApplicationEventPublisher
- 이벤트를 발행하고 구독하는 모델을 편리하게 지원
- 이벤트 프로그래밍에 필요한 인터페이스 제공. 옵저버 패턴 구현체.
- Annotation 지원
  - `@EventListener`
  - `@Async`(`@EnableAsync` 설정 필수)
  - `@Order`

#### ResourceLoader
- 리소스를 읽어오는 기능을 제공하는 인터페이스
  - 파일 시스템에서 읽어오기
  - 클래스패스에서 읽어오기
  - URL로 읽어오기
  - 상대/절대 경로로 읽어오기


## @Configuration
- CGLIB 바이트코드 조작 라이브러리를 통해 `AppConfig` 클래스를 상속받은 `AppConfig$$xxxCGLIB`를 등록
- `AppConfig`에서 `@Bean`으로 등록되어 있는 메서드 내용을 호출하는 것이 아닌 `CGLIB`에 의해 조작된 코드 내의 메소드 내용을 호출

