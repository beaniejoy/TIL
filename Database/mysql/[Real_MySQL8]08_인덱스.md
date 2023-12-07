# Chpap 8. 인덱스

## I/O

- Random I/O (랜덤)
- Sequential I/O (순차)

> - 일반적으로 쿼리를 튜닝하는 것은 랜덤 I/O 자체를 줄여주는 것이 목적  

MySQL에서 `인덱스 레인지 스캔 >> 랜덤 I/O` / `풀 테이블 스캔 >> 순차 I/O`  
그래서 큰 테이블에서 레코드의 대부분을 읽는 작업에서 인덱스를 사용하지 않고 풀 스캔을 유도할 때도 있다.

## Clustered & Non-clustered Index

- [관련 참고 링크(그림 아주 좋음)](https://velog.io/@gillog/SQL-Clustered-Index-Non-Clustered-Index)