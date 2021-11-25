# Transaction

## 개념

- 데이터베이스의 데이터를 조작하는 작업의 단위
- ACID 원칙을 보장해야 한다.

### ACID
- `Atomicity`(원자성)
  - all or nothing
  - 작업이 일부분만 성공하는 일이 없도록 보장
  - 모든 작업이 성공됐을 때 DB에 업데이트가 이루어지고 하나라도 실패시 다시 rollback
- `Consistency`(일관성)
  - 트랜잭션 종료시 DB의 제약조건에 맞는 상태를 일관되게 보장하는 성질
- `Isolation`(격리성)
  - 트랜잭션이 진행되는 중간에 다른 트랜잭션이 진행되고 있는 트랜잭션 중간 상태 데이터를 볼 수 없도록 보장하는 성질
- `Durability`(영속성)
  - 트랜잭션 커밋 이후에 결과가 영구적으로 적용됨을 보장하는 성질

## Isolation Level

> **isolation level** (**격리수준**)  
> 동시에 여러 트랜잭션이 데이터를 변경하거나 조회할 때, 한 트랜잭션의 작업 내용이 다른 트랜잭션에 어떻게 보여질지를 결정하는 기준

- ACID 원칙을 strict하게 지키기가 힘들다 -> 동시성이 매우 떨어짐
- **transaction isolation 전략을 통해 ACID를 적절히 희생해 동시성을 얻을 수 있다.**
- 여기서 발생할 수 있는 세 가지 문제 상황 존재
  - `dirty read`: 다른 트랜잭션에서 COMMIT 되지 않은 데이터를 읽어옴(신뢰성 하락)
  - `non-repeatable read`: 한 트랜잭션 내부에서 여러번 SELECT 쿼리를 실행했을 때 일관성 있는 결과가 안나옴
  - `phantom read`: 한 트랜잭션 내부에서 SELECT 여러번 진행했을 시 이전에 없던 row가 읽혀오는 상황

### READ UNCOMMITED

- 해당 트랜잭션 내부에선 `SELECT` 쿼리 실행시 아직 commit 되지 않은 데이터를 읽어올 수 있다.
- `Dirty Read` 발생 가능
```sql
-- Transaction A.
START TRANSACTION;
INSERT INTO member(id, name, age) VALUES(1, 'beanie', 20);

-- Transaction B. (READ UNCOMMITED)
SELECT * FROM member WHERE id = 1;

-- Transaction A.
ROLLBACK;
```
- `Tx B.` 에서 존재하지도 않는 데이터를 읽어들인 셈이다.(`Dirty Read`)
- 이외에도 `Non-repeatable Read`, `Phantom Read` 발생 가능

### READ COMMITTED

- commit된 데이터만 보이는 수준의 격리수준 보장
- `REPEATABLE READ`와 다르게 read(`SELECT`) 작업마다 DB snapshot을 다시 뜬다.
- `Non-repeatable Read`, `Phantom Read` 발생 가능

### REPEATABLE READ
- `MySQL InnoDB`의 기본 isolation level
- `READ COMMITED`와 비슷하게 Consistent Read를 위해 lock을 걸지 않고 snapshot을 따온다.
- `READ COMMITED`과 다른 점은 처음 read(SELECT) 때 snapshot을 찍고 그 이후에는 첫 snapshot을 읽어온다.

> This is the default isolation level for InnoDB.  
> Consistent reads within the same transaction read the snapshot established by the first read.

- 여기서 특이한 점

###  SERIALIZABLE
- 가장 타이트한 level
- `SELECT` 쿼리를 모두 `SELECT ... FOR SHARE` (S Lock)으로 변환
- `dirty read`, `non-repeatable read`, `phantom read` 발생할 가능성이 가장 적다.
- `DEAD LOCK`(교착 상태)에 빠질 가능성이 상당히 높아서 주의해서 사용

```sql
-- Transaction A.
START TRANSACTION;
UPDATE 

-- Transaction B. (READ UNCOMMITED)
SELECT * FROM member WHERE id = 1;

-- Transaction A.
ROLLBACK;
```

> MySQL InnoDB commit 특징
> - InnoDB는 일단 실행된 모든 쿼리를 DB에 적용한다.(commit 상관없이)
> - 그래서 Consistent Read를 위해 log에 기록하고 log를 통해 특정 시점의 DB snapshot을 복구해서 가져와야 한다.