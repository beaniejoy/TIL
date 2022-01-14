# Collection 함수

- 코틀린 컬랙션에서 사용되는 람다함수들 기록, 정리

## map

### mapNotNull
```kt
inline fun <T, R : Any> Array<out T>.mapNotNull(
    transform: (T) -> R?
): List<R>
```
- 주어진 변환 함수(`transform`)를 원래 컬랙션의 각 요소에 적용한 null이 아닌 결과 만 포함하는 목록을 반환
- `transform` 반환값이 null인지 아닌지가 중요

```kt
val searchKeywordMap = convertedMap.mapNotNull { 
        (key, value) -> value?.let { key to it } 
    }.toMap()
```
- Map에서 value가 null인 경우 제외하는 코드
- value가 null이 아닌 경우 `key to it`(value)를 통해 Map에 다시 담는다.
  - 일종의 필터링을 통해 걸러진 데이터를 새로운 Map 객체에 다시 담는 것

## filterValues
```kt
@Suppress("UNCHECKED_CAST")
fun <K, V> Map<K, V?>.filterNotNullAndBlankValues(): MutableMap<K, V> =
    filterValues { it != null && (it as? String) != "" } as MutableMap<K, V>
```
- `Filter a Kotlin Map to get non-null values only` Reference 참조
- param value가 null이거나 blank(`""`)인 경우에 filtering하는 함수

## Reference
- [Filter a Kotlin Map to get non-null values only](https://blog.jdriven.com/2020/10/filter-a-kotlin-map-to-get-non-null-values-only/)