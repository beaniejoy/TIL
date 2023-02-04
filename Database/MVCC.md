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
                            write(x : 50)
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
- `serializable` 
  - 이 단계에서는 DB마다 다르다. (결과는 `repeatable read`와 같음)
  - **MySQL**: MVCC보다 Lock 기반으로 동작(operation마다 Shared Lock 적용)
  - **PostgreSQL**: **SSI**(Serializable Snapshot Isolation)기법이 적용된 MVCC 적용
- `read uncommitted`
  - MVCC는 commit된 데이터를 읽기 때문에 보통 MVCC가 적용되지 않는다.
    - **MySQL**: MVCC가 적용되는 isolation level은 `read committed`, `repeatable read` 두 개에 해당
    - **PostgreSQL**: read uncommitted level을 적용해도 read committed level로 동작