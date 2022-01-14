# Kotlin nullable

- 코틀린은 자바와 다르게 null에 대해서 컴파일 단계에서 체크를 한다. (그래서 좀 더 복잡하지만 익숙해지면 오히려 더 편리함?)

## javaClass

- 코틀린에서 어떤 타입인지 알아내고자 할 때 `javaClass`를 사용
```kt
testMap["key"]?.javaClass // testMap: Map<String, Any?>
```
- 

## Not Null 선언(`!!`)
```kt
package chap7.delegate

class Email(val address: String)

fun loadEmails(person: Person): List<Email> =
    listOf(Email("beaniejoy@example.com"), Email("joy@example.com"))


class Person(val name: String) {
    private var _emails: List<Email>? = null
    val emails: List<Email>
        get() {
            if (_emails == null) {
                _emails = loadEmails(this)
            }

            // _emails 변수는 nullable type 인데 return type은 not null type이다.
            // !!를 통해 not null을 확정해야 한다.
            return _emails!!
        }
}
```
- `_emails`은 nullable type
- `emails`은 not null type
- `return _emails!!` 에서 `!!`를 통해 not null임을 확정해야 한다.
- nullable과 not null type은 다른 타입이라 생각하면 된다.