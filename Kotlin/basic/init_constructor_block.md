# init & constructor block 관련

primary constructor와 init block 간에 순서에 있어서 헷갈린 것이 있어 기록  

아래 코드 하나만 알면 된다.

```kotlin
open class Parent {
    private val a = println("Parent.a - #4")

    constructor(arg: Unit=println("Parent primary constructor default argument - #3")) {
        println("Parent primary constructor - #7")
    }

    init {
        println("Parent.init - #5")
    }

    private val b = println("Parent.b - #6")
}

class Child : Parent {
    val a = println("Child.a  - #8")

    init {
        println("Child.init 1 - #9")
    }

    constructor(arg: Unit=println("Child primary constructor default argument - #2")) : super() {
        println("Child primary constructor - #12")
    }

    val b = println("Child.b - #10")

    constructor(arg: Int, arg2:Unit= println("Child secondary constructor default argument - #1")): this() {
        println("Child secondary constructor - #13")
    }

    init {
        println("Child.init 2 - #11")
    }
}
```
**주의해야 될 부분은 property에 직접 값을 할당하는 부분이 init 보다 위에 있으면  
init 보다 먼저 수행되니 참고**

## 참고

[[Kotlin] 코틀린 constructor vs init block](https://tourspace.tistory.com/122)