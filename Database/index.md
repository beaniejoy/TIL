# INDEX

인덱스(주로 MySQL) 관련된 모든 정리 내용

<br>

## :pushpin: Read DISC

```
CPU - Cache Memory - Main Memory - HDD
```

- 왼쪽부터 처리속도가 빠른 순으로 나열
- `CPU`, `Cache`, `Main Memory`는 전자식 `HDD`는 기계식 (속도와 처리방식의 차이로 병목현상 발생)
  - SSD(Solid State Drive) 개발 -> 전자식 저장매체 (TPS에서 HDD와 어마어마한 차이)

<br>

## :pushpin: I/O

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

## :pushpin: INDEX 개념

인덱스는 정렬된 자료구조, 탐색 범위를 최소화 시켜준다.

- 칼럼의 값(data)을 key, 해당 레코드 주소값을 value로 하는 한쌍의 인덱스를 가리킴
- 이러한 key - value 쌍을 key 기준으로 sorting 처리한다.
- 테이블에 새로운 데이터를 저장할 때 기존의 테이블 저장방식보다 더 큰 비용 발생(sorting으로 인해)
- 대신 SELECT와 같은 조회처리에서 월등히 높은 성능 자랑  
  (**저장성능을 포기하는 대신 조회성능을 가져가는 trade-off 발생**)

<br>

## :pushpin: Index 자료구조

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

## :pushpin: Index Algorithms

- **B-Tree 인덱스**
  - 가장 보편적인 인덱스 알고리즘
  - 칼럼의 값을 보존하면서 해당 값을 이용해 인덱싱
  - Balanced Tree 구조 (Not Binary)
  - 이진 트리 형태의 보완된 구조  
  (이진 트리형태는 한쪽으로 치우쳐진 그림도 가능하기에 검색에서 비효율적인 구조 발생 가능)
  
- Hash 인덱스
  - 칼럼의 값으로 해시 값을 계산, 이를 이용한 인덱싱
  - 본래 값을 변형해서 인덱싱하기에 값의 일부를 이용한 검색은 불가
  - 주로 메모리 기반 데이터베이스에서 사용

- Fractal-Tree 인덱스
  - B-Tree 단점을 보완
  - 데이터 저장, 삭제 때 처리비용을 줄일 수 있도록 설계

<br>

## :pushpin: B-Tree 인덱스 사용에 영향을 미치는 요소

B-Tree, B+Tree는 인덱스 구성하는 칼럼 크기, 레코드 건수, 유니크한 인덱스 키 값 개수 등에 의해  
조회, 쓰기 작업 성능에 영향을 받는다.

### 인덱스 키 값의 크기 > **페이지**

InnoDB 스토리지 엔진 페이지(블록) 개념이 존재  
페이지: 디스크의 모든 읽기 및 쓰기 작업의 최소 작업 단위  
(즉, 한 번 읽고 쓸 때(I/O) 페이지 크기만큼 블록단위로 처리하게 된다.)  

InnoDB는 default로 `**16KB**`  
(`innodb_page_size` 시스템 변수에서 크기 지정 가능, 4 ~ 64KB 범위 내로 변경 가능)  

예시로 B-Tree 인덱스에서 노드 내의 키-값 쌍 하나를 보면  
```
`key` 16 bytes | `value` 12 bytes
```
계산해보면 `16 * 1024 / (16 + 12) = 585개` >> 자식노드를 585개를 가질 수 있는 B-Tree가 된다.  
여기서 key에 해당하는 인덱스 대상이 되는 테이블 칼럼 데이터의 길이가 길어지면 가질 수 있는 자식노드 개수가 줄어든다.  
(ex. key 용량이 16KB > 32KB로 늘어난다고 했을 때, `16 * 1024 / (32 + 12)` = 372개로 줄어듬, 이렇게 되면 1번 Disk Random IO 발생할 것을 2번 이상 발생할 수도 있다는 얘기가 된다.)  

즉 인덱스 대상이 되는 칼럼의 데이터 길이가 늘어난다는 것은 B-Tree 인덱스 성능이 떨어지는 결과를 발생시킬 수 있기에 잘 조절해야 한다.

### B-Tree Depth(깊이)


<br>

## :pushpin: Clustered Index

- 클러스터 인덱스는 데이터 위치를 결정하는 키 값
  - ArrayList 처럼 기존 인덱스 테이블 중간에 pk 값이 들어간다면 그 뒤의 모든 pk 값에 해당하는 데이터 주소 값들이 한 칸식이 밀리게 된다. (`PK - 데이터 주소`)
  - 클러스터 키 순서에 따라 데이터 저장 위치가 변경(키 삽입/갱신 시에 성능이슈 발생)
- MySQL의 PK는 클러스터 인덱스
  - **PK로 auto_increment vs UUID 설정할시 성능 차이가 있는지 찾아보기**
- MySQL에서 PK를 제외한 모든 인덱스는 PK를 가지고 있다.
  - 인덱스의 리프노드에 value 값으로 pk 값을 가지고 있음
  - pk 제외한 secondary index만으로 데이터를 직접 찾아갈 수 없음(항상 pk 인덱스를 검색해야 함)
  - 리프노드에 데이터 주소를 가지고 있으면 데이터 순서가 변경될 때 인덱스 테이블도 갱신해야 하니 pk로 지정(**pk 값 자체는 변경되거나 삭제될 일이 없기에 >> 이부분 알아봐야할 듯**)
- pk 활용시 장점
  - pk를 활용한 검색이 빠름, 특히 범위 검색
    - pk는 정렬되어 있기 때문에 공간적인 cache 이점이 존재
  - 세컨더리 인덱스들이 pk를 가지고 있어 커버링에 유리(??)