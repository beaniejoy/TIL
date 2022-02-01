# JPA JUnit Test

## Test 기본 설정

```java
@SpringBootTest
@ActiveProfiles("test)
public class QuerydslTest {...}
```
- `@SpringBootTest` 설정은 모든 bean 객체를 등록한다.

```java
@DataJpaTest
@ActiveProfiles("test")
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class QuerydslTest {...}
```
- https://webcoding-start.tistory.com/20 참고

## EntityManager 설정

```java
@Autowired
TestEntityManager testEntityManager;

EntityManager em;

@BeforeEach
void init() {
    em = testEntityManager.getEntityManager();
}
```