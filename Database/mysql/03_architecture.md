# Architecture

## :pushpin: InnoDB Storage Engine Architecture

- 유일하게 레코드 기반 잠금 제공 > 높은 동시성 처리 가능

### InnoDB Buffer Pool
- 캐싱 기능
  - 디스크 데이터 파일
  - 인덱스 정보
- 쓰기 지연 기능
  - INSERT, UPDATE, DELETE 등의 변경 쿼리를 모아서 처리
  - **랜덤한 디스크 작업 횟수를 줄일 수 있어 성능 개선(이해 X)**
- `innodb_buffer_pool_size`
- 관련 Reference
  - [InnoDB Buffer Pool](https://flashsql.github.io/innodb-doc-kr/blog/innodb/5.1.buffer-pool.html)