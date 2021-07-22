# ArrayList, LinkedList 비교

## ArrayList

- 용량(capacity)을 변경해야 될 때 비효율적
- 저장할 데이터의 개수를 잘 고려해 충분한 용량의 인스턴스를 준비한다면 효율적

## LinkedList

- 배열(Array)에서 크기를 변경할 수 없다는 점, 비순차적인 데이터 추가, 삭제의 비효율성이라는 단점 존재
- 이를 보완한 것이 `LinkedList`

```java
public class Node {
  Node next;      // 다음 노드의 주소
  Node previous;  // 이전 노드의 주소
  Object obj;     // 데이터
}
```

- 단방향 링크드 리스트보다 더블 링크드 리스트를 많이 사용  
여기서 더 나아가 더블 서큘러 링크드 리스트를 사용함
- 새로운 데이터 추가 혹은 삭제할 때 데이터 복사없이 작업이 이루어져 상당히 효율적  
(데이터 복사만 이루어지지 않는다면 `ArrayList`가 더 효율적일 수 있음)

## ArrayList, LinkedList 비교

- 순차적으로 추가, 삭제하는 경우 `ArrayList`가 빠름
- 중간에 데이터 추가 or 중간에 있는 데이터를 삭제하는 경우 `LinkedList`가 빠름  
(`LinkedList`는 노드 간 연결만 다시 설정하면 되지만 `ArrayList`는 하나의 데이터 때문에 그 뒤에 있는 모든 데이터들을 재배치해야됨)
