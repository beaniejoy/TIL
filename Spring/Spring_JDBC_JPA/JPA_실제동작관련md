# JPA 실제 동작 관련

<br>

## :pushpin: @OneToOne 이슈

### batch_size의 의도치 않은 in query 조회

#### 문제상황
```kt
// PrayerLapseJpaEntity.kt
@Comment("대표 기도 제목 ID")
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "cover_prayer_id")
var coverPrayer: PrayerJpaEntity? = null
    protected set
```
연관관계 주인인 PrayerLapse가 cover_prayer_id 필드로 Prayer을 가지고 있음   
단방향 연관관계로 Prayer에는 따로 OneToOne이 설정 X

```kt
val prayerLapseEntity = prayerLapseRepository.findByIdOrNull(prayerLapseId)
    ?: throw RuntimeException("${ErrorCategory.PRAYER_LAPSE}: 기도 제목 경과가 존재하지 않습니다.")
```
연관관계 주인인 `PrayerLapse` 단건 조회시 Prayer 조회 쿼리 진행 X (**LAZY 정상 동작**)  

```kt
val prayerEntity = prayerRepository.findByIdOrNull(prayerId)
    ?: throw RuntimeException("${ErrorCategory.PRAYER}: 기도 제목이 존재하지 않습니다.")
```
그 이후 `Prayer` 단건 조회인데 여기서 의도치 않은 select in query 발생  
`Prayer` 단건 조회시 id=4 로 조회했는데 PrayerLapse의 OneToOne으로 가지고 있던 cover_prayer_id=6 인 상황

```sql
select * from prayerhouse.prayers where id in (4, 6, null, ..., null)
```
위의 쿼리가 나가게 된다. (batch_size 지정한 만큼 null로 채워져서 나감)  
`default_batch_fetch_size` 설정을 없애면 의도한 쿼리대로 실행됨  

> 원인은 명확히 모르겠으나 global batch_size 설정시 `@OneToOne` proxy로 가져온 prayer에 대해서 Prayer 단건 조회시 해당 proxy로 가져온 식별자값과 함께 in query로 실행되는 것이 아닌가 추측만 하고 있는 상황

#### 해결 방법

```kt
@Comment("대표 기도 제목 ID")
@Column(name = "cover_prayer_id")
var coverPrayerId: Long? = null
    protected set
```
OneToOne 연결을 없애고 엔티티에 단순 id 필드로 해서 따로 조회하도록 했음  

<br>

## :pushpin: N + 1 문제

JPA 가장 큰 성능 문제 중 하나인 N + 1 문제에 대해서 정리

### 개념
특정 Entity 조회시 
- root Entity 조회 쿼리(1)
- 연관관계의 Entity 조회 쿼리(N)

이렇게 조회 쿼리가 root 쿼리 외에 연관관계로 설정된 다른 entity에 대해서 조회하기 위해 추가 쿼리가 나가는 것을 `N + 1` 문제라 한다.  

보통 `N + 1`보다 `1 + N`이 더 정확한 표현인 것 같다.  
root 조회 쿼리(1) + root 쿼리에서 나온 데이터 개수대로 연관관계 조회 쿼리(N)

### 발생하는 상황

#### 1. LAZY proxy로 가져올 때

```kotlin
// Order
@Entity
@Table(name = "orders")
class Order protected constructor() {
    @Id
    @GeneratedValue
    @Column(name = "order_id")
    var id: Long = 0L

    @OneToMany(mappedBy = "order", fetch = FetchType.LAZY)
    var orderItems: MutableList<OrderItem> = ArrayList()
}
```
- fetch LAZY mode로 가져오는 경우
- 애플리케이션 단에서 Order Entity를 조회해서 실제 orderItem 필드를 사용하는 경우

```kotlin
@GetMapping("/api/v2/orders")
fun orderV2(): List<OrderDto> {
    val orders = orderRepository.findAllByString(OrderSearch())
    // LAZY proxy 초기화
    return orders.map {
        OrderDto.of(it)
    }
}
```
- 위와 같이 `OrderDto.of` 메소드를 통해서 Order Entity의 orderItems를 가져오는 경우 해당 proxy가 초기화되면서 order_items 테이블 추가 조회 쿼리가 나가게 된다.

#### 2. JPQL로 조회할 때

```kotlin
em
.createQuery("selct o from Order o", Order::class.java)
.resultList
```
- `Order` entity에 Member entity가 EAGER 페치 전략으로 설정되어 있을 때
- JPQL로 `Order` entity 단순 조회
- JPQL 보고 분석해서 SQL 쿼리 작성(`select * from order`)
- JPA가 쿼리 결과내용을 보고 Order Entity 작성
- `Order`의 `Member` 엔티티가 EAGER로 묶여있음. 해당 엔티티도 조회해야 한다.
- `Member` 엔티티를 영속성 컨텍스트에서 찾고 없으면 쿼리 요청
- 결국 `Order` 개수(`N`)만큼 Member 조회 쿼리를 요청하게 된다.
  - `select * from Member where id = ?`

> EAGER로 묶였다 해도 N + 1 문제가 완전하게 해결된 것은 아닌 것이다.  
> 상황에 따라 N + 1 문제 충분히 발생 가능