# Bean 설정 방법

## xml 설정
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

## xml + component scan
```xml
<context:component-scan base-package="com.example.springapplication.cafe" />
```
등록할 빈에 대해서 `@Component`가 있어야 한다.

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

## Configuration + component scan
```java
@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
```
DemoApplication 클래스가 위치한 곳부터 하위 패키지로 component scan 한다.  
(Spring Boot에 특화된 방법, `@SpringBootApplication`)
