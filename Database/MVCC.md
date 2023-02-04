# MVCC

- Multi-Version Concurrency Control

<br>

## :pushpin: 등장 배경
- `Lock based Concurrency Control` 환경에서는 전체 처리량이 좋지 못하다. (성능 이슈)

- Lock table

|| read  | write |
| :---: | :---: | :---: |
| read  |   O   |   X   |
| write |   X   |   X   |

- read-read 제외한 다른 쌍에서는 무조건 다른 한 쪽 tx locking(blocking) 발생
- 성능상 좋지 못하다.
- 여기서 등장하게 된 것잉 MVCC

<br>

## :pushpin: 특징

- Lock table

|| read  | write |
| :---: | :---: | :---: |
| read  |   O   |   O   |
| write |   O   |   X   |
- write-write 쌍 제외하고는 lock을 허용
- lock based 동시성 제어환경보다 성능상 좋다.

### 주요 특징
1. 데이터를 읽을 때 **특정 시점 기준**으로 가장 최근의 commit된 데이터를 읽음
   - **MySQL**에서는 이것을 `Consistent Read`라고 한다.
2. 데이터 변화(write) 이력을 관리한다.
   - write 발생하면 바로 DB table에 반영하는 것이 아닌 DB 내부 어딘가에 변화 이력을 임시 저장하고 commit 때 반영한다.
3. read, write는 서로를 block하지 않는다.

<br>

## :pushpin: Isolation Level별 MVCC 동작 방식

```
init> x = 50

Tx 1.                       Tx 2.
                            write(x : 50) ... 여기서 write lock을 먼저 걸고 write 수행
read(x) > 10
                            commit
read(x) > ? ... (1)
```
- `(1)`에서 읽히는 x 값은 isolation level에 따라 다르다.
  - `read committed`: **50** 
    - read operation 기준으로 그 전에 commit된 데이터
  - `repeatable read`: **10**
    - tx 시작 시간 기준으로 그 전에 commit된 데이터
    - DB마다 시점이 다를 수 있다. (최초 read 기준인 경우도 있음)
  - **여기서는 MVCC 주요 특징 1번째 해당**
- `read uncommitted`
  - MVCC는 commit된 데이터를 읽기 때문에 보통 MVCC가 적용되지 않는다.
    - **MySQL**: MVCC가 적용되는 isolation level은 `read committed`, `repeatable read` 두 개에 해당
    - **PostgreSQL**: read uncommitted level을 적용해도 read committed level로 동작
- `serializable` 
  - 이 단계에서는 DB마다 다르다. (결과는 `repeatable read`와 같음)
  - **MySQL**: MVCC보다 Lock 기반으로 동작(operation마다 Shared Lock 적용)
  - **PostgreSQL**: **SSI**(Serializable Snapshot Isolation)기법이 적용된 MVCC 적용

<br>

## :pushpin: DB별 Isolation Level 동작 방식

- `Tx 1.`: x가 y에 40을 이체한다.
- `Tx 2.`: x에 30을 입금한다.

### PostgreSQL에서의 동작 방식
- `Tx 1.`, `Tx 2.` 둘다 read committed
```
init> x = 50, y = 10

Tx 1.                       Tx 2.
read(x) > 50
write(x : 10)
                            read(x) > 50
                            wrtie(x : 80) ... x에 대한 lock을 얻지 못하고 대기
read(y) > 10
write(y : 50)
commit ... x,y write lock 해제
                            wrtie(x : 80)
                            commit
```
위의 상황에서 최종결과는 `x = 80, y = 50`이 된다. 예상했던 결과인 `x = 40, y = 50`와 불일치  
`Tx 1.`에서 x update한 내용이 lost되는 상황 발생 > 이것을 `lost update`라고 한다.

- `Tx 1.` read committed, `Tx 2.` repeatable read
```
init> x = 50, y = 10

Tx 1.                       Tx 2.
read(x) > 50
write(x : 50)
                            read(x) > 50
                            wrtie(x : 80) ... x에 대한 lock을 얻지 못하고 대기
read(y) > 10
write(y : 50)
commit ... x,y write lock 해제
                            wrtie(x : 80) ... operation fail
                            rollback
```
PostgreSQL에서 repeatable read는 **같은 데이터에 먼저 update한 tx가 commit 되면 이후 tx는 rollback**  
이후 write에 대해서 rollback 이루어지기 때문에 **lost update** 문제를 방지할 수 있음
(Tx 1.에도 `repeatable read`로 적용하는 것이 좋다.)  

### MySQL에서의 동작 방식
- `Tx 1.`, `Tx 2.` 둘다 repeatable read
```
init> x = 50, y = 10

Tx 1.                       Tx 2.
                            read(x) > 50 ... (1)
read(x) ... (2)
                            write(x : 80)
                            commit
read(x) > 80 ... (3)
write(x : 40)
read(y) > 10 ... (4)
write(y : 50)
commit ... (5)
```
(1)... **locking read**로 읽는다.(`SELECT ... FROM account WHERE id = 'x' FOR UPDATE`)  
(2)... (1)에서 locking read로 읽었기 때문에 blocking 상태로 **대기모드**, (2)에서도 **locking read**로 읽는다.  
(3)... repeatable read level이라 x는 50으로 예상할 수 있으나 MySQL에서는 **locking read는 가장 최근에 commit된 데이터를 읽는다.**  
(4)... 여기서도 locking read로 읽는다.
(5)... 마지막 commit 이후로 `x = 40, y = 50`이 된다.  

> MySQL에서는 repeatable read만으로는 lost update 방지하는데 부족하기 때문에  
> **locking read**로 보완(이부분은 MySQL에서 자동으로 붙여주는 것이 아니라 개발자가 직접 붙여줘야 한다.)  

```sql
-- locking read
SELECT ... FROM ... FOR UPDATE; -- write lock(exclusive lock)
SELECT ... FROM ... FOR SHARE; -- read lock(share lock)
```
