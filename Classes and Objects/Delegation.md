## 위임

### implementation by delegation

위임 패턴은 상속의 좋은 대안임이 증명되었습니다. Kotlin 은 장식 코드(boilerplate code) 없이 언어 자체에서 위임 패턴을 지원합니다. 아래에서 Derived 클래스는 Base 인터페이스에서 상속받은 모든 public 메서드를 지정한 객체로 위임할 수 있습니다.

~~~kotlin
interface Base{
    fun print()
}

class BaseImpl(val x: Int) : Base{
    override fun print{
        print(x)
    }
}

class Derived(b: Base) : Base by b

fun main(){
    val b = BaseImpl(10)
    Derived(b).print() // 10 출력.
}
~~~

Derived 의 상위타입 목록의 by 절은 Derived 객체 내부에 b 를 저장하고 컴파일러가 Base 의 모든 메서드에 대해 b 로 위임하는 메서드를 Derived 에 생성한다는 것을 나타냅니다.

