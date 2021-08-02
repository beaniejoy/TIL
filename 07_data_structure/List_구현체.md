# List와 구현체

## List

- 순서 고려, 중복은 허용
- key 중요 X
- 메모리를 적게 쓴다는 장점이 존재(어떻게 구현하는지에 따라 다름)
- 순서대로 반복해서 작업하고 싶을 때 이를 사용하면 된다.
- List에서 중요한 구현체: `ArrayList`, `LinkedList`

## ArrayList

- `Array`와 같이 Random Access가 가능
- 특정 위치 탐색할 때 유용(ex. 홀수 번째 데이터만 조회하는 경우)
- 중간부터 접근이 가능하기에 탐색하는 과정에 있어서 효율적
- 일정 capa를 넘어서는 add가 이루어질 경우 capa를 늘림
- 중간에서 삽입, 삭제하는 경우 비효율적(`System.arraycopy`가 이루어지기에 조심)
- 마지막을 기준으로 삽입, 삭제하는 경우는 `LinkedList`보다 효율적
- 저장할 데이터의 개수를 잘 고려해 충분한 용량의 인스턴스를 준비한다면 효율적
- [링크 참고](https://beaniejoy.tistory.com/50)

## LinkedList

- 처음 head부터 차례로 찾아나간다.
- 배열(`Array`)에서 크기를 변경할 수 없다는 점, 비순차적인 데이터 추가, 삭제의 비효율성이라는 단점 존재
- 이를 보완한 것이 `LinkedList`
- element 개수가 가변적일 때 이것을 사용하는 것이 효율적(size 예측이 불가한 경우)

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

## ArrayList & LinkedList 비교

  |              | ArrayList | LinkedList |
  | :----------: | :-------: | :--------: |
  |   get/set    |   O(1)    |    O(n)    |
  |  add(시작)   |   O(n)    |    O(1)    |
  |   add(끝)    |   O(1)    |    O(1)    |
  |  add(일반)   |   O(n)    |    O(n)    |
  | remove(시작) |   O(n)    |    O(1)    |
  |  remove(끝)  |   O(1)    |    O(1)    |
  | remove(일반) |   O(n)    |    O(n)    |

- 순차적으로 추가, 삭제하는 경우 `ArrayList`가 빠른 경우가 있음
- 중간에 데이터 추가 or 중간에 있는 데이터를 삭제하는 경우 `LinkedList`가 빠름  
(`LinkedList`는 노드 간 연결만 다시 설정하면 되지만 `ArrayList`는 하나의 데이터 때문에 그 뒤에 있는 모든 데이터들을 재배치해야됨)
- 중간 데이터 추가하는 경우는 `LinkedList`도 `O(n)`, 해당 index까지 탐색하는데 비용 발생

## 정리

- `ArrayList`는 정해진 크기에서 끝지점부터 순차적으로 add, remove하는 경우 성능이 좋다.
- `ArrayList`는 Random Access가 가능하기에 특정 위치 탐색에 유리
- `LinkedList`는 양끝단에 삽입, 삭제가 자주 발생할 때 사용하면 좋다.
- `ArrayList`가 `LinkedList`보다 공간복잡도 측면에서 효율적이다.