# 영속성 관리 관련 정리

## 쓰기 지연(write behind)

### 벌크 연산

- `deleteAllByIdInBatch`
```java
public static final String DELETE_ALL_QUERY_BY_ID_STRING 
= "delete from %s x where %s in :ids";
```
in query를 이용한 bulk delete 기능  
`deleteAllByIdInBatch` 내부 구현코드를 보면 `executeUpdate()`를 통해 쿼리 실행하게 된다.  
중요한 특징은 이러한 벌크 연산은 영속성 컨텍스트를 무시하고 DB에 직접 쿼리  
> 즉, 쓰기지연 기능도 사용할 수 없고, 영속성 컨텍스트에도 변경된 내용들이 반영이 안되기 때문에 주의해서 사용해야 한다.