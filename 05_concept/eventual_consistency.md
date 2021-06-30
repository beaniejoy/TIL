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

특정 도메인 객체의 변경사항이 발생할 때 단일 트랜잭션 내에서 동일 DB내의 Outbox 테이블에 관련 정보를 저장  
Outbox에 저장된 데이터들을 토대로 후속 이벤트 처리

- 단일 Microservice내 DB update 이슈 발생
- 해당 update 이벤트를 Outbox 테이블 저장
- MessageRelay를 통해 Outbox 데이터의 이벤트들을 Message Broker로 발행
- Outbox는 일종의 Message Queue 역할을 수행