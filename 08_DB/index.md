# INDEX

## Read DISC

```
CPU - Cache Memory - Main Memory - HDD
```

- 왼쪽부터 처리속도가 빠른 순으로 나열
- `CPU`, `Cache`, `Main Memory`는 전자식 `HDD`는 기계식 (속도와 처리방식의 차이로 병목현상 발생)
  - SSD(Solid State Drive) 개발 -> 전자식 저장매체 (TPS에서 HDD와 어마어마한 차이)

## INDEX 개념

- 칼럼의 값(data)을 key, 해당 레코드 주소값을 value로 하는 한쌍의 인덱스를 가리킴
- 이러한 key - value 쌍을 key 기준으로 sorting 처리한다.
- 테이블에 새로운 데이터를 저장할 때 기존의 테이블 저장방식보다 더 큰 비용 발생(sorting으로 인해)
- 대신 SELECT와 같은 조회처리에서 월등히 높은 성능 자랑  
  (저장성능을 포기하는 대신 조회성능을 가져가는 trade-off 발생)

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

## B-Tree Index

- Balanced Tree 구조 (Not Binary)
- 이진 트리 형태의 보완된 구조  
  (이진 트리형태는 한쪽으로 치우쳐진 그림도 가능하기에 검색에서 비효율적인 구조 발생 가능)
