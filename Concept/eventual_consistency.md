# "Eventual or Strong" Consistency

기존 모놀리틱 아키텍처에서보다 분산화된 서비스 아키텍쳐(MSA) 환경에서 이슈 발생  
MSA 환경 내에서는 여러 서버와 DB가 존재하므로 ACID 특성을 이용해 일관성 유지하기가 힘들다.

## Strong Consistency

- traditional relational databases에서 사용되는 컨셉
- 데이터 수정이 완료될 때까지 replica된 DB의 해당 데이터 읽기를 제한
- 중간 과정에서 에러가 발생한다면 그 동안의 모든 과정이 rollback되어 초기화됨.

## Eventual Consistency

- Eventual consistency makes sure that data of each node of the database gets consistent eventually
- 데이터 일치성이 되는데까지 걸리는 시간이 정해져있진 않지만 최종적으로 데이터 일치성이 보장된다는 의미
- 데이터가 최신으로 업데이트 되지 않았어도 읽기를 허용. 최종적으로 업데이트된 내용을 반환.
- `DNS System`: Eventual Consistency의 잘 알려진 예시

### Why Eventual Consistency?

> MSA 환경에서 Order 서비스 예시

`주문 - 결제 - 창고 - 배송`

- 만약 배송에서 에러가 발생한다면
- 결제 과정에서 에러가 발생한다면
- 창고 재고처리 과정에서 문제가 발생한다면

여러 돌발 에러 상황에 맞이할 수 있다.  
Strong Consistency로 진행한다면 모든 과정이 rollback되어 처음부터 다시 처리되어야 함.  
하지만 이미 실제 창고에서 재고처리가 됐다던가 결제가 이미 완료가 된 상황이라면 문제는 더 커짐.  
이 때 Eventual Consistency를 도입한다면 주문을 시작하고 그 다음 과정에서 최종적으로 완료되어 처리될 것이라 생각하고 주문완료 처리를 할 수 있음.

## Transactional Outbox Pattern

![image2019-9-9_10-25-10-1](https://user-images.githubusercontent.com/41675375/124500838-71c1fb00-ddfb-11eb-8438-5e1e646ef0d9.png)

특정 도메인 객체의 변경사항이 발생할 때 단일 트랜잭션 내에서 동일 DB내의 Outbox 테이블에 관련 정보를 저장  
Outbox에 저장된 데이터들을 토대로 후속 이벤트 처리

![image2019-6-19_10-0-25-600x321](https://user-images.githubusercontent.com/41675375/124500886-84d4cb00-ddfb-11eb-87d4-955b4bb63cb4.png)

- 단일 Microservice내 DB update 이슈 발생
- 해당 update 이벤트를 Outbox 테이블 저장
- MessageRelay를 통해 Outbox 데이터의 이벤트들을 Message Broker로 발행
- Outbox는 일종의 Message Queue 역할을 수행
- 데이터베이스 트랜잭션과 메시징은 하나의 트랜잭션으로 다루기 때문에 메시지 발행 시차는 존재해도 결과적으로 메시지발행과 상태변경 간에 일관성을 유지할 수 있음  
(`eventual consistency`)

## At Least Once, At Most Once, Exactly Once

### At Least Once
- 적어도 한번 이상 (무조건 한번 이상은 이벤트 발생처리)
- **최소한 한 번 메세지 전송 보장**으로 Sender 가 메세지를 전송하고 일정 시간 내에 Receiver 에게 ACK 를 받지 못하면 다시 메세지를 전송하는 방식
- 메시지 중복 발생 가능성이 높음 (네트워크 지연 등)

### At Most Once
- Sender 는 메세지를 한 번만 전송하고 Receiver 가 받았는지 안받았는지 관심 X 
- 지연, 유실 모두 발생할 수 있으면 꼭 메세지를 받지 않아도 되는 경우에 사용할 수 있음

### Exactly Once
- 정확하게 중복없이 메세지를 한 번만 전달하는 방식
- 메세지 필터링 해주는 Middleware 가 존재(부가적인 기능이 추가되어야 함)
- 상대적으로 Middleware의 존재로 구현의 어려움이 있음
- 완벽하게 한 번만 전달한다고 보장하지는 못함


## Reference

- [EXACTLY-ONCE & AT-LEAST-ONCE DELIVERY](https://yenaworldblog.wordpress.com/2019/09/09/exactly-once-at-least-once-delivery/)
- [msa에서 메시징 트랜잭션 처리하기](https://www.popit.kr/msa%EC%97%90%EC%84%9C-%EB%A9%94%EC%8B%9C%EC%A7%95-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0)