# bulk insert 관련

JPA만으로 bulk insert를 구현하기 어려워 JDBC template을 사용하는 경우가 존재  
특히 배치 작업 시

```java
public void bulkInsert(List<Post> posts) {
  var sql = String.format("""
    INSERT INTO `%s` (memberId, contents, createdDate, createdAt)
    VALUES (:memberId, :contents, :createdDate, :createdAt)
    """, TABLE);

  SqlParameterSource[] params = posts.stream()
    .map(BeanPropertySqlParameterSource::new)
    .toArray(SqlParameterSource[]::new);

  namedParameterJdbcTemplate.batchUpdate(sql, params);
}
```

```sql
INSERT INTO `Post` (memberId, contents, createdDate, createdAt)
VALUES 
(1, 'eOMtThyhVNLWUZNRcBaQKxI', '2023-12-23', '2023-12-24 03:23:51.990899'),
(1, 'yedUsFwdkelQbxeTeQOvaScfqIOOmaa', '2023-12-31', '2023-12-09 02:31:44.6088'),
...
```