# Mockito

## JUnit4, 5 주의
```kt
@ExtendWith(MockitoExtension::class)
internal class StudyServiceTest {
    @Mock
    private lateinit var mockMemberService: MemberService

    @Mock
    private lateinit var mockStudyRepository: StudyRepository

    @Test
    fun createStudyTest() {
        val studyService = StudyService(mockMemberService, mockStudyRepository)
        assertNotNull(studyService)
    }
}
```
- JUnit5 용 mock 설정시 `@Test` JUnit5 패키지에서 가져오는거 확인해야함
  - JUnit5: `org.junit.jupiter.api.Test`
  - JUnit4: `org.junit.Test`
- @ExtendWith 하고 JUnit5 버전 안맞춰주면 다음과 같은 에러 발생
```text
kotlin.UninitializedPropertyAccessException: 
lateinit property ... has not been initialized
```