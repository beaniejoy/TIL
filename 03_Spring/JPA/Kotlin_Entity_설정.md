# Gradle Setup for Kotlin Entity

## Entity 관련 Kotlin gradle 설정 내용
```gradle
apply plugin: "kotlin-jpa"
apply plugin: "kotlin-noarg"
apply plugin: "kotlin-allopen"

```

- `kotlin-jpa` 플러그인은 `kotlin-noarg` 플러그인을 사용하여, `@Entity`, `@Embeddable`, `@MappedSuperClass` 어노테이션이 붙어있는 클래스에 기본 생성자를 자동으로 생성해줍니다.
- `kotlin-allopen` 플러그인을 적용합니다. Hibernate에서는 final클래스를 사용하면 안 되기 때문에 해당 플러그인을 이용하여 자동으로 open 상태로 만들어 줍니다.

```gradle
allOpen { 
    annotation("javax.persistence.Entity") 
    annotation("javax.persistence.MappedSuperclass")
    annotation("javax.persistence.Embeddable")
}
```
- Entity class를 `data class`로 작성하는 것은 추천하지 않음

## Entity 클래스 예시

```kotlin
@Entity
@Table(name = "coupon")
class Coupon(
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long = 0L,

    @Column(name = "use_min_amount")
    val useMinAmount: BigDecimal,

    @Column(name = "discount_amount")
    val discountAmount: BigDecimal,

    @Column(name = "usable_from")
    val usableFrom: LocalDateTime,

    @Column(name = "usable_until")
    val usableUntil: LocalDateTime,

    @Enumerated(EnumType.STRING)
    @Column(name = "status")
    var status: Status = Status.NORMAL,

    @Column(name = "member_id")
    var memberId: Long = 0L
) : BaseTimeEntity() {
    //...
}
```
- 이런 식으로 Entity 객체 생성 가능
- `val id: Long = 0`: default 값을 설정해줌으로써 코틀린에서 Coupon 생성자를 통해 Entity 객체 생성시 id 값 없이 생성할 수 있다.
- 위의 no-arg 플러그인이 작동하기 때문에 기본 생성자를 알아서 만들어준다.

## 생각해볼 것들
- Entity에는 위에서 볼 수 있듯이 특정 필드에 대해서 생성자 생성시 값을 부여하면 안되는 것들이 있다.
  - id: 보통 JPA Generation 전략을 사용하기 때문에 생성자에서 해당 값을 부여하지 않아도 된다.
  - Status: 최초 Coupon 생성 시 NORMAL로 분류해야 하기 때문에 생성자에서 default로 지정할 수 있다.
  - 이 때는 `constructor()`를 활용해도 되지 않을까?