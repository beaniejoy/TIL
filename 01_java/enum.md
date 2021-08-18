# Java enum 관련 필기

## enum에 Collection 적용

```java
public enum OrderStatus {
  COMPLETED("주문완료"),
  PAYMENT_WAITING("결제대기", OrderStatus.COMPLETED.status),
  PAYMENT_COMPLETED("결제완료", OrderStatus.PAYMENT_WAITING.status),
  ARRANGED("배송준비", OrderStatus.PAYMENT_COMPLETED.status),
  DELIVERY("배송중", OrderStatus.ARRANGED.status),
  ARRIVAL("배송완료", OrderStatus.DELIVERY.status),
  CANCELED("주문취소",
          OrderStatus.COMPLETED.status,
          OrderStatus.PAYMENT_COMPLETED.status,
          OrderStatus.PAYMENT_COMPLETED.status,
          OrderStatus.ARRANGED.status);

  private final String status;
  private final Set<String> handlingStatusList;

  OrderStatus(String status, String... handlingStatus) {
      this.status = status;
      this.handlingStatusList = new HashSet<>(Arrays.asList(handlingStatus));
  }

  //...
  
}
```

## Reference

- [Java Enum 활용기 - 우아한형제들 기술블로그 (by 이동욱님)](https://techblog.woowahan.com/2527/)