# Mockk 관련 내용

```kotlin
testImplementation("io.mockk:mockk:${Version.Deps.MOCKK}")
```


## Injections

```kotlin
@ExtendWith(MockKExtension::class)
class RegisterPrayerServiceTest(
    @MockK
    private val mockPrayerLapseStorePort: PrayerLapseStorePort,
    @MockK
    private val mockMemberReaderPort: MemberReaderPort,
    @MockK
    private val mockPrayerStorePort: PrayerStorePort,
    @InjectMockKs
    private val registerPrayerService: RegisterPrayerService
) {
  //...
}
```


## stub

`@MockK`로 mocking 객체 주입받은 것에 대해서는 해당 객체의 메소드가 호출되는 로직이 있는 경우  
stubbing 꼭 필수

```kotlin
every { mockMemberReaderPort.getNotNullById(MEM_BEANIE_ID) }
  .returns(ACTIVATED_MEM_BEANIE)
justRun {
  mockPrayerLapseStorePort.updateCoverPrayer(
    id = eq(TEST_PRAYER_LAPSE_ID),
    coverPrayer = any()
  )
}
```
- every ~ return을 통해 원하는 return type의 객체로 stubbing 가능
- justRun은 주로 void method에 대해서 stubbing하기 위함

stubbing할 때 parameter에 대해서 테스트 대상이 되는 로직 내부에서 객체가 생성되어 파라미터 주입되는 경우에 대해서 `any()`로 해야 한다.  
왜냐하면 객체 생성될 때마다 다른 참조값의 새로운 객체가 생성되기 때문에 테스트 코드에서 직접 생성한 객체로 파라미터 지정하면 오류 발생