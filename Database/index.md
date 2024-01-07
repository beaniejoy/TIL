# INDEX

인덱스(주로 MySQL) 관련된 모든 정리 내용

<br>

## Read DISC

```
CPU - Cache Memory - Main Memory - HDD
```

- 왼쪽부터 처리속도가 빠른 순으로 나열
- `CPU`, `Cache`, `Main Memory`는 전자식 `HDD`는 기계식 (속도와 처리방식의 차이로 병목현상 발생)
  - SSD(Solid State Drive) 개발 -> 전자식 저장매체 (TPS에서 HDD와 어마어마한 차이)

<br>

## I/O

- Random I/O (랜덤)
- Sequential I/O (순차)

HDD는 원판(플래터)을 이동시켜 디스크 헤드로 이동시켜야 하는 비용이 발생,
랜덤 IO가 여러번 발생하면 플래터를 이곳 저곳 이동시키게 된다.  
순차 IO는 디스크를 한 번만 이동시켜도 됨(**플래터에는 chunk 단위로 읽기 때문 >> 좀더 알아보기**)
(SDD도 랜덤, 순차 IO간 성능차이 발생하는 것은 마찬가지)

> **일반적으로 쿼리를 튜닝하는 것은 랜덤 I/O 자체를 줄여주는 것이 목적(Real MySQL)**

MySQL에서 `인덱스 레인지 스캔 >> 랜덤 I/O` / `풀 테이블 스캔 >> 순차 I/O`  
그래서 큰 테이블에서 레코드의 대부분을 읽는 작업에서 인덱스를 사용하지 않고 풀 스캔을 유도할 때도 있다.

관련 링크
- [순차(Sequential) I/O와 랜덤(Random) I/O](https://velog.io/@ddangle/%EC%88%9C%EC%B0%A8Sequential-IO%EC%99%80-%EB%9E%9C%EB%8D%A4Random-IO)

<br>

## INDEX 개념

인덱스는 정렬된 자료구조, 탐색 범위를 최소화 시켜준다.

- 칼럼의 값(data)을 key, 해당 레코드 주소값을 value로 하는 한쌍의 인덱스를 가리킴
- 이러한 key - value 쌍을 key 기준으로 sorting 처리한다.
- 테이블에 새로운 데이터를 저장할 때 기존의 테이블 저장방식보다 더 큰 비용 발생(sorting으로 인해)
- 대신 SELECT와 같은 조회처리에서 월등히 높은 성능 자랑  
  (**저장성능을 포기하는 대신 조회성능을 가져가는 trade-off 발생**)

<br>

## Index 자료구조

MySQL index는 B+Tree 자료구조를 사용
- 삽입, 삭제시 항상 균형을 이룬다. (Balanced Tree)
- 하나의 노드가 여러 개의 자식 노드를 가질 수 있음 (B-Tree, B+Tree 공통)
- 리프노드에만 데이터 존재
  - 연속적인 데이터 접근 시 유리(B+Tree는 리프노드만 데이터, 나머지는 데이터를 찾아가기 위한 키(??))
  - [B-Tree는 인접한 리프노드간 연결 X / B+Tree는 연결 존재](https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html)
- MySQL은 특징이 Index 자료구조 마지막 리프노드에는 PK를 담고 있다. (실제 데이터 주소를 가리키는 다른 DB와 차이점)
- MySQL에서 리프노드의 PK값을 가지고 Clustered Index를 조회하게 됨

관련 링크
- [B-Tree 모델 탐색](https://www.cs.usfca.edu/~galles/visualization/BTree.html)
- [B+Tree 모델 탐색](https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html)

<br>

## Index Algorithms

- **B-Tree 인덱스**
  - 가장 보편적인 인덱스 알고리즘
  - 칼럼의 값을 보존하면서 해당 값을 이용해 인덱싱
- Hash 인덱스
  - 칼럼의 값으로 해시 값을 계산, 이를 이용한 인덱싱
  - 본래 값을 변형해서 인덱싱하기에 값의 일부를 이용한 검색은 불가
  - 주로 메모리 기반 데이터베이스에서 사용
- Fractal-Tree 인덱스
  - B-Tree 단점을 보완
  - 데이터 저장, 삭제 때 처리비용을 줄일 수 있도록 설계

<br>

## B-Tree Index

- Balanced Tree 구조 (Not Binary)
- 이진 트리 형태의 보완된 구조  
  (이진 트리형태는 한쪽으로 치우쳐진 그림도 가능하기에 검색에서 비효율적인 구조 발생 가능)
  
