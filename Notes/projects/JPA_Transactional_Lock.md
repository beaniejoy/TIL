# JPA를 이용한 Lock


## @Lock

```java
public interface PlaceReviewCountRepository extends JpaRepository<PlaceReviewCount, Long> {
    // WRITE LOCK 부여
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    Optional<PlaceReviewCount> findByPlaceId(UUID placeId);
}
```

- write lock(`PESSIMISTIC_WRITE`)을 JPA에 적용하기 위해서 JPA Repository 단에서 걸어주어야 한다.  
- Entity class에 거는 방법도 있는데 주로 낙관적 잠금에만 해당한다.


## READ_COMMITTED, SELECT FOR UPDATE 관계?

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
//...
@Lock(LockModeType.PESSIMISTIC_WRITE)
```
- 두 방법의 차이점은? (알아볼 것)

### READ_COMMITTED

- row에 record lock을 거는 형태  
  (이게 shared lock인지 exclusive lock인지 알아야할 듯)

```sql
SELECT * FROM point_ex WHERE place_id = [장소1]; -- Transaction A.
SELECT * FROM point_ex WHERE place_id = [장소1]; -- Transaction B.

...

UPDATE ... -- Transaction A.
UPDATE ... -- Transaction B.
```
- 처음 SELECT에서 여행지에 대한 첫 리뷰 확인 체크
- 여기서 A, B가 동시에 장소1에 대한 첫 리뷰 여부 조회 시 모두 `null`을 반환할 수 있다.  
  (둘 다 첫 리뷰 대상자로 인식)
- 이 때 해당 SELECT 쿼리에 대해서 lock을 걸어 다른 트랜잭션에서 동시에 조회를 못하도록 해야 하는데 `READ_COMMITTED`가 적할할까?

## READ_COMMITTED는 SELECT문 끼리의 접근 제한을 두지 않는다.

- 위의 예시대로 Transaction A와 B가 동시에 SELECT문에 접근
- `READ_COMMITTED`는 동시에 접근하는 것을 막진 않는다.
- 단지 COMMIT되지 않는 것을 read하는 `dirty read`를 방지하기 위해 Undo 영역의 데이터를 읽는 것일 뿐 조회하는 것 자체를 Lock을 걸 수 없다.
- 이 때는 write lock이 필요해보임