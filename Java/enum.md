# Java enum 관련 필기

## enum에 Collection 적용

```java
public enum OrderStatus {
  CANCELED("주문취소"),
  ARRIVAL("배송완료"),
  DELIVERY("배송중",
          OrderStatus.ARRIVAL.status),
  ARRANGED("배송준비",
          OrderStatus.DELIVERY.status),
  PAYMENT_COMPLETED("결제완료",
          OrderStatus.ARRANGED.status,
          OrderStatus.CANCELED.status),
  PAYMENT_WAITING("결제대기",
          OrderStatus.PAYMENT_COMPLETED.status,
          OrderStatus.CANCELED.status),
  COMPLETED("주문완료",
          OrderStatus.PAYMENT_WAITING.status,
          OrderStatus.CANCELED.status);

  private final String status;
  private final Set<String> covertToList;

  OrderStatus(String status, String... covertToStatus) {
      this.status = status;
      this.covertToList = new HashSet<>(Arrays.asList(covertToStatus));
  }

  //...
  
}
```
enum class는 enum 상수의 순서가 있어서 먼저선언된 상수 내부에 뒤에 나올 상수내용을 적용하려하면 에러 발생한다.
(ordinal 존재, 순서 지켜야함)

## Reference

- [Java Enum 활용기 - 우아한형제들 기술블로그 (by 이동욱님)](https://techblog.woowahan.com/2527/)