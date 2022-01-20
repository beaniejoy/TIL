# Lambda 관련 내용

## Errors

### 람다 캡처링

> `람다 캡처링` : 기존 메소드의 스택 변수들에 대해 참조가 가능하도록 람다의 스택에 복사하는 과정

```
Variable used in lambda expression should be final or effectively final
```
- 자바 람다함수 내부에서 바깥쪽 변수를 가지고 와서 사용해야 될 때 발생할 수 있는 에러

```java
Member member = memberRepository.findTop1ByOrderByIdDesc();
Long newActionId = member == null ? 1L : member.getActionId() + 1;

/*
이런 방식은 아래 람다 안에서 newActionId를 적용할 때 컴파일 에러 발생

Long newActionId = 1L;
if (member != null) {
    newActionId = member.getActionId() + 1;
}
*/

managerRepository.findAllById(...).forEach(manager->{
    managerRepository.save(manager.toManagerRoleMapHistory(newActionId, ...));
});
```
- 람다를 사용하면 `람다 캡처링` 발생
- 람다 내부 코드를 위한 Stack 영역을 따로 생성해서 기존에 Stack에 있던 변수들을 그대로 복사
  - 기존 Stack의 변수 중 매핑하고 있는 Heap 영역이 있으면 똑같이 해당 Heap 영역에 접근(주소값 복사)
- 즉 `newActionId`라는 값을 복사한 람다전용 Stack이 구성되는데 문제는 해당 람다 내부는 변수에 대한 수정이 불가능하다.
  - **이 새로운 스택은 같은 변수를 똑같이 들고만 있는다는 내용이 중요**
- 그래서 람다내에서 값을 수정할 수 없는 `final`변수, `effectively final`변수만 사용 가능
- 위 예제에서는 newActionId를 조건문에 따라 새로 할당하는 것 자체가 `final` 변수가 아니기 때문에 컴파일 에러 발생하는 것