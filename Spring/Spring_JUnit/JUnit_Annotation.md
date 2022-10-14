# JUnit Annotation 관련

## @RunWith

- `@RunWith` 는 JUnit4에서 사용되는 것으로 JUnit 프레임워크의 테스트 실행 방법을 확장할 때 사용
- `SpringJUnit4ClassRunner`라는 테스트 컨텍스트 프레임워크 확장 클래스가 존재
  - 이것을 확장하면 JUnit이 테스트 진행하는 중에 테스트가 사용할 ApplicationContext 생성과 관리하는 작업을 진행해줌
  - `@ContextConfiguration`: 자동으로 만들어질 ApplicationContext의 설정파일 위치를 지정
- 스프링 JUnit 확장기능은 테스트가 실행되기 전에 딱 한번만 ApplicationContext를 만들어두고, 테스트 오브젝트가 만들어질 때 마다 ApplicationContext를 테스트 오브젝트 특정 필드에 주입

## @SpringBootTest

- **스프링의 모든 빈을 로드해서 테스트하는 방식**
- JUnit4 사용시 `@RunWith(SpringRunner.class)`를 사용했었음
- JUnit5 사용시 `@ExtendWith`로 변경되었는데 `@SpringBootTest`를 사용하면 생략 가능

```java
// JUnit4
@RunWith(SpringRunner.class)
class ControllerTest {
...
}

// JUnit5
// @ExtendWith(SpringExtension.class)
@SpringBootTest
@AutoConfigureMockMvc
class ControllerTest {
...
}
```

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@BootstrapWith(SpringBootTestContextBootstrapper.class)
@ExtendWith({SpringExtension.class})
public @interface SpringBootTest {
    @AliasFor("properties")
    String[] value() default {};
...
}
```

## @WebMvcTest
```java
@WebMvcTest
class CafeControllerTest {

    @Autowired
    MockMvc mvc;

    @MockBean
    CafeService cafeService;

    //...
}
```

- `@WebMvcTest(테스트 대상 클래스.class)`
- 인자로 지정한 클래스에 대해서만 실제로 빈을 주입받아 테스트 진행
  - `Controller` 클래스를 대상으로 함
- 아규먼트로 컨트롤러를 지정해주지 않으면 `@Controller` `@RestController` `@ControllerAdvice` 등등 컨트롤러와 연관된 bean들이 로드
- 컨트롤러 이외에는 Mock 객체를 주입받아 테스트 진행
  - `@Autowired` `MockMvc` 주입
  - `@MockBean` 으로 Service 단 가짜 객체 주입
- **`@SpringBootTest`와 달리 컨트롤러 관련 코드만 단위 테스트하고자 할 때 사용**

## @DataJpaTest

```java
@DataJpaTest
@ActiveProfiles("test")
```
- 별도의 스프링 빈을 등록하지 않고, Entity, EntityManager 정도만 등록해서 테스트
- in-memory DB를 활용해서 테스트가 실행
- @Transactional > 테스트 종료시 자동으로 롤백