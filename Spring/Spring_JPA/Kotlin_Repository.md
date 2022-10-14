# Kotlin으로 Repository 구성하기

```kt
interface MemberRepository : JpaRepository<Member, Long> {
    fun findAll(spec: Specification<Member>, pageable: Pageable): Page<Member>

    fun findMemberById(id: Long): Member?
}
```

- java에서 사용하던 `Optional`을 굳이 사용하지 않고도 사용 가능
- kotlin에서 유용하게 사용되는 nullable 처리를 사용하면 됨
- `Optional<Member>` -> `Member?` 이렇게 반환타입을 바꿀 수 있음

```kt
// Service
fun findMember(id: Long) {
  val member = memberRepository.findMemberById(id)
    ?: throw RuntimeException("사용자를 찾을 수 없습니다.")
}
```

- 해당 쿼리메소드를 사용할 때 nullable 처리를 사용해서 Exception을 던질 수 있다.
