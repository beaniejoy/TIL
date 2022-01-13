
```kt
@Component
class UserSpec {
    companion object {
        fun searchWith(keywordMap: Map<String, Any>): Specification<BalanceRecord> {
            return Specification<BalanceRecord> { root, query, criteriaBuilder -> null }
        }
    }
}

@Component
object UserSpec {
    fun searchWith(keywordMap: Map<String, Any>): Specification<BalanceRecord> {
        return Specification<BalanceRecord> { root, query, criteriaBuilder -> null }
    }
}
```
- 위의 두 케이스가 어떻게 다른 것인지 알아보기
- object 용법과 companion object 사용법이 어떻게 다른 것인지