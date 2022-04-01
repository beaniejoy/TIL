# Convert Type

- 코틀린에서 타입간 변환에 대한 기록

## Object to Map

```kt
fun RequestDto.convertToMap(): Map<String, Any?> {
    val mapper = jacksonObjectMapper()
    val returnMap = mutableMapOf<String, Any?>()
    return mapper.convertValue(this, returnMap::class.java)
}
```
- `jacksonObjectMapper`를 사용
- convert 변환 후 타입 지정을 위해 `HashMap` 오브젝트 생성

## Map에서 value의 타입 관련 필기 내용
```kt
val convertedMap = request.convertToMap()
val searchKeywordMap = convertedMap.filterNotNullAndBlankValues()

// class java.lang.Long
println("############# ${searchKeywordMap["id"]?.javaClass}") 

// Any?
val balanceId = searchKeywordMap["id"] 
```
- 위의 상황에서 `.javaClass` 사용시 Long으로 변환되는 것을 볼 수 있다.
- 그리고 다시 balanceId key로 index 접근했을 때 `Any?` 반환
- javaClass 할 때 type casting이 되는 건지 궁금