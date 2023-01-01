# N + 1 문제

JPA 가장 큰 성능 문제 중 하나인 N + 1 문제에 대해서 정리

## :pushpin: 개념
특정 Entity 조회시 
- root Entity 조회 쿼리(1)
- 연관관계의 Entity 조회 쿼리(N)

이렇게 조회 쿼리가 root 쿼리 외에 연관관계로 설정된 다른 entity에 대해서 조회하기 위해 추가 쿼리가 나가는 것을 `N + 1` 문제라 한다.  

보통 `N + 1`보다 `1 + N`이 더 정확한 표현인 것 같다.  
root 조회 쿼리(1) + root 쿼리에서 나온 데이터 개수대로 연관관계 조회 쿼리(N)

<br>

## :pushpin: 발생하는 상황

### 1. LAZY proxy로 가져올 때

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

### 2. JPQL로 조회할 때

```kotlin
em
.createQuery("selct o from Order o", Order::class.java)
.resultList
```
- Order entity에 Member entity가 EAGER 페치 전략으로 설정되어 있을 때
- JPQL로 Order entity 단순 조회
- JPQL 보고 분석해서 SQL 쿼리 작성(`select * from order`)
- JPA가 쿼리 결과내용을 보고 Order Entity 작성
- Order의 Member 엔티티가 EAGER로 묶여있음. 해당 엔티티도 조회해야 한다.
- Member 엔티티를 영속성 컨텍스트에서 찾고 없으면 쿼리 요청
- 결국 Order 개수(`N`)만큼 Member 조회 쿼리를 요청하게 된다.
  - `select * from Member where id = ?`

> EAGER로 묶였다 해도 N + 1 문제가 완전하게 해결된 것은 아닌 것이다.  
> 상황에 따라 N + 1 문제 충분히 발생 가능