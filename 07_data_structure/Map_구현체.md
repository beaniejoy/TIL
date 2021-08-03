# Map

## HashMap

> [HashMap에 대한 자세한 설명 참고(d2.naver)](https://d2.naver.com/helloworld/831311)

- `Hashtable`과 같이 거론됨
- `Hashtable` : `HashMap` = `Vector` : `ArrayList` 관계 비슷
- key - value 쌍의 하나의 데이터(entry)로 저장되는 형태

```java
public class HashMap extends AbstractMap implements Map, Cloneable, Serializable {
  transient Entry[] table;
  //...
  static class Entry implements Map.Entry {
    // key, value 쌍
    final object key;
    Object value;
  }
}
```
- `key`: 유일한 값, `value`: 중복허용

## Hashing

- `HashSet`, `HashMap`에서 사용(사실 `HashSet`은 내부적으로 `HashMap`으로 작동한다.)
- **hash function을 이용해 hash table에 저장하고 검색하는 것을 의미**
- `hash function`: 임의의 길이의 데이터를 고정된 길이의 데이터로 매핑하는 함수(wiki)

### Hash Table
- 해싱에서 사용하는 해시테이블의 자료구조는 배열과 LinkedList의 조합으로 구성
- 저장할 데이터의 키값을 해시함수로 digest하고 그 결과로 나온 값을 기준으로 배열을 탐색
- 탐색한 배열의 특정 위치에 해당하는 해시 버킷에 저장된다. (LinkedList 구조)

### Hash Collision

```java
int hashFunction(Object key) {
  return key.hashCode() % M;
}
```
- 1/M 확률로 같은 hash function 결과값이 나올 수 있다.
- 하나의 해시 버킷 공간이라면 다른 두 개의 key값에 대해 접근을 해야하므로 충돌 발생

#### Hash Collision 해결하는 방법
- **Open Addressing**(Linear Probing)
- **Separate Chaining**(LinkedList)
  - java `HashMap`에서 택한 방법
  - 하나의 버킷에 너무 많은 데이터들이 연결되면 최악의 경우 `O(n)`만큼 시간복잡도 발생 가능 
  - 분산되어 저장되는 것이 효율적

### `equals()`, `hashCode()` Overriding
- Object의 `equals()`는 기본적으로 hashCode를 기준으로 한다.
- 만약 어떤 클래스에서 `equals()`를 Overriding 하면 문제 발생 가능  
  - `hashCode()`를 해시함수로 사용
  - Overriding된 `equals()`로 인해 비교 대상 두 객체가 같은 것으로 인식
  - 그런데 `hashCode()`는 다르면 다른 해시 버킷에 저장될 가능성이 크다.
  - 값은 같은데 다른 것으로 인식(모순), 이렇기에 둘다 Overriding한다.

