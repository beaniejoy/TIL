# Convert Type

- 코틀린에서 타입간 변환에 대한 기록

## Object to Map

```kt
fun RequestDto.convertToMap(): Map<String, Any?> {
    val mapper = jacksonObjectMapper()
    val returnMap = HashMap<String, Any?>()
    return mapper.convertValue(this, returnMap::class.java)
}
```
- `jacksonObjectMapper`를 사용
- convert 변환 후 타입 지정을 위해 `HashMap` 오브젝트 생성