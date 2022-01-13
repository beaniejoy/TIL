
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