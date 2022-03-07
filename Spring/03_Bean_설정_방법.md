# Bean 설정 방법

<br>

## XML 설정
```xml
<bean id="cafeService" class="해당경로.CafeService">
  <property name="cafeRepository" ref="cafeRepository" />
</bean>

<bean id="cafeRepository" class="해당경로.CafeRepository />
```

```java
ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");

CafeService cafeService = context.getBean("cafeService", CafeService.class);
```
- 위와 같이 하면 bean 등록을 위해 XML 설정에서 굉장히 번거로운 작업을 수행해야  
  (하나하나 작성해야함)

<br>

## XML + component scan
```xml
<context:component-scan base-package="com.example.springapplication.cafe" />
```
```java
// @Service
@Component
public class CafeService {
    @Autowired
    CafeRepository cafeRepository;
}

// @Repository
@Component
public class CafeRepository {
    //...
}
```
- XML에 bean을 등록하지 않아도 됨
- 등록할 빈에 대해서 `@Component`가 있어야 한다.
- 외부 주입을 위해서 `@Autowired`(`@Inject`) 사용해야함
  - 생성자, setter, field 영역에 사용 가능

<br>

## Configuration 설정

```java
@Configuration
public class ApplicationConfig {

    @Bean
    public CafeRepository cafeRepository() {
        return new CafeRepository();
    }

    @Bean
    public CafeService cafeService(CafeRepository cafeRepository) {
        CafeService cafeService = new CafeService();
        cafeService.setCafeRepository(cafeRepository);
        return cafeService;
    }
}
```

```java
ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
```

<br>

## Configuration + component scan
```java
@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
```
DemoApplication 클래스가 위치한 곳부터 하위 패키지로 component scan 한다.  
(Spring Boot에 특화된 방법, `@SpringBootApplication`)
