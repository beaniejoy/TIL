# where 1=1 지양

```sql
SELECT *
FROM company
WHERE 1=1
<if test="name != null">
  name = #{name}
</if>
```
- 조회하고 싶은 name을 가지고 index 스캔을 통해 데이터를 검색하고자 한다.
- name을 실수로 입력하지 않은 상황에서 위의 쿼리는 company table을 전체 스캔을 하게 된다.
(상당한 비효율 쿼리를 생산하게 된다.)
- 되도록 조건절에 이런 `1=1`을 넣지 말 것 