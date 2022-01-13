# 스프링 왜 쓸까

- 객체지향관점
- 의존관계
- BeanFactory
  - ApplicationContext
    - EnvironmentCapable
    - MessageSource
    - ApplicationEventPublisher
    - ResourceLoader
- @Configuration

## 객체지향 관점

### SRP
- 단일 책임 원칙에 기반한 코드 구현을 가능하게 한다.
- 스프링 없이 단순 자바 코드로 서비스 객체를 구성하려고 할 때 클라이언트가 되는 객체는 여러 역할을 수행하게 된다.
  1. 사용할 객체에 대한 구현 객체를 직접 생성
  2. 직접 생성한 객체들을 연결
  3. 연결하고 이들을 실행
- SRP 관점에서는 올바르지 못한 형태
- 구현 객체를 생성하고 연결하는 책임은 스프링이 담당 (AppConfig, 어노테이션 방식 등)
- 클라이언트 객체는 실행하는 책임만 담당

### DIP, OCP

- 클라이언트 객체에서 `A interface`를 구현하는 `A1Impl`, `A2Impl` 구현 객체가 있다고 했을 때
```java
// 클라이언트 객체의 필드 값 직접 주입
A a = A1Impl();
```
- 이런 방식일 때 혹여나 `A2Impl`로 교체를 해야할 때 클라이언트 코드를 수정해야 하는 경우 발생
- 즉, 적용되는 구현 객체의 교체를 위해 클라이언트 코드를 수정해야 하는 상황이 발생
- 이를 방지하고자 스프링에서 대신 이러한 의존관계를 관리함으로써 교체 또한 스프링 차원에서 수정하면 됨(AppConfig, 어노테이션 방식 등)

> 기술적인 부분에서 확장에는 열려 있지만 해당 기술의 사용 영역의 변경은 닫혀있다는게 핵심  
> (DIP, OCP 구현)

## 의존 관계

- 정적인 의존관계
  - 코드상 `import` 관계를 바로 알 수 있는 관계
  - `A interface`를 구현하고 있는 구현 클래스는 어떤 것이 있는지 파악 가능
  - 하지만 실제 runtime에서 어떤 구현 객체가 사용되는지는 알 수 없다.
  - `클래스 다이어그램` 파악 가능
- 동적인 의존 관계
  - 실제 runtime 환경(실행 시점)에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존과계가 연결되는 것을 의존관계 주입(`DI`)
  - `객체 다이어그램` 파악 가능
- DI(Dependency Injection)
  - **스프링의 의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고 동적인 객체 인스턴스 의존관계를 쉽게 변경 가능**


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

