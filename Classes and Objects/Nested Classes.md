## 중첩 클래스와 내부 클래스

다른 클래스에 클래스를 중첩(nested)할 수 있습니다.

~~~kotlin
class Outer{
    private val bar: Int = 1
    class Nested{
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
~~~

### inner classes

Inner 로 지정하면 외부(outer) 클래스의 멤버로 클래스에 접근할 수 있습니다. 내부 클래스는 외부 클래스 객체에 대한 레퍼런스를 갖습니다.

~~~kotlin
class Outer{
    private var bar: Int = 1
    inner class Inner{
        fun foo() = bar
    }
}

val demo = Outer.Inner().foo() // == 1
~~~

