## 위임 프로퍼티

필요할 때 수동으로 기능을 구현할 수 있지만, 한 번에 다 구현하고 라이브러리에 넣는 것이 매우 좋은 그런 종류의 프로퍼티가 존재합니다. 다음 프로퍼니가 이런 종류에 속합니다.

- lazy 프로퍼티 : 최초에 접근할 때 값을 계산.
- observable 프로퍼티 : 이 프로퍼티가 바뀔 때 리스너에 통지.
- 별도 필드가 아닌 맵에 저장한 프로퍼티

이를 포함한 여러 경우를 처리하기 위해 Kotlin 은 _ 위임 프로퍼티(Delegated Properties) _ 를 지원합니다.

~~~kotlin
class example{
    var p: String by Delegate()
}
~~~

위임 프로퍼티 구문은 val/var < property name >: < Type > by < expression > 입니다. by 뒤에 식이 _ 대리 객체(delegate) _ 이며, 프로퍼티에 대한 get() 과 set() 을 대리 객체의 getValue() 와 setValue() 메서드로 위임핮니다. 프로퍼티 대리 객체는 인터페이스를 아무렇게나 구현하면 안 되고, getValue() 함수를(var 의 경우 setValue() 함수를) 제공해야 합니다.

~~~kotlin
class Delegate{
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String{
        return "$thisRef, thank you for delegating '${prroperty.name}' to me!"
    }
    
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String){
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
~~~

 Delegate 인스턴스에 위임하는 프로퍼티 p 에서 값을 읽으면 Delegate 의 getValue() 함수를 실행합니다. 이 메서드의 첫 번째 파라미터는 p 를 포함한 객체이고 두번째 파라미터는 p 자체에  대한 설명을 포함합니다.(예를 들어, 프로퍼티의 이름을 구할 수 있습니다.) 다음은 예입니다.

~~~kotlin
val e = Example()

print(e.p)
~~~

이 코드는 다음을 출력합니다.

Example@33a17727, thank you for delegating ‘p’ to me!

비슷하게 p 에 할당하면 setValue() 함수를 호출합니다. 처음 두 파라미터는 동일하고 세 번째 파라미터는 할당할 값을 찾습니다.

~~~kotlin
e.p = "NEW"
~~~

이 코드는 다음을 출력합니다.

NEW has been assigned to 'p' in Example@33a17727.

### property delegate requirements

위임 객체의 조건을 아래에 요약했습니다.

- thisRef - _ 프로퍼티 소유자 _ 와 같거나 상위타입.(확장 프로퍼티의 경우 - 확장한 타입)
- property  - KProperty < * > 타입이거나 그 상위타입.

이 함수는 프로퍼티와 같은 타입(또는 그 하위타입을)을 리턴해야 합니다.

**수정 가능** 프로퍼티(var)에 대해 위임 객체 이름이 setValue() 이고 추가로 다음 파라미터를 갖는 함수를 제공해야 합니다.

- receiver - getValue() 와 동일.
- metadata - getValue() 와 동일.
- new value -  프로퍼티와 같거나 상위타입이어야 합니다.

getValue() 와 setValue() 함수는 위임 클래스나 멤버 함수나 확장 함수로 제공할 수 있습니다. 확장의 경우 원래 이 함수를 제공하지 않는 객체에 프로퍼티를 위임해야할 때 유용합니다. 두 함수 모두 operator 키워드로 지정해야 합니다.

### Standard Delegates

Kotlin 표준 라이브러리는 몇 가지 유용한 종류의 위임 객체를 위한 팩토리 메서드를 제공합니다.

#### Lazy

lazy() 는 람다를 파라미터로 받고 Lazy < T >  인스턴스를 리턴하는 함수입니다. Lazy 는 lazy 프로퍼티를 구현하기 위한 위임 객체입니다. 처음 get() 을 호출하면 lazy() 에 전달할 람다를 실행하고 그 결과를 저장하며 이후 get() 호출에 대해선 저장한 결과를 리턴합니다.

~~~kotlin
val lazyValue: String by lazy{
    println("computed!")
    "Hello"
}

fun main(args: Array<String>){
    println(lazyValue)
    println(lazyVaule)
}
~~~

lazy 프로퍼티 연산은 기본적으로 **동기화**됩니다. 한 스레드만 값을 계산할 수 있으며 모드 스레드는 같은 값을 보게 됩니다. 만약 초기화 과정을 동기화할 필요가 없다면 lazy() 함수의 파라미터에 LazyThreadSafetyMode.PUBLICACTION 을 전달해서 여러 스레드가 동시에 실행할 수 있도록 할 수 있습니다. 그리고 항상 한 스레드에서만 초기화하는 것을 보장하려면 LazyThreadSafetyMode.NONE 모드를 사용하면 됩니다. 이 모드는 스레드 안정성을 보장하기 위한 오버헤드를 발생하지 않습니다.

#### observable

Delegates.observable() 은 초깃값과 수정에 대한 핸들러를 인자로 갖습니다. 프로퍼티에 값을 할당할 때마다(할당 완료 후에) 핸들러를 호출합니다. 핸들러는 할당 대상 프로퍼티, 이전 값, 새로운 값을 파라미터로 받습니다.

~~~kotlin
import kotlin.properties.Delegates

class User{
    var name: String by Delegates.observable("<no name>")	[
        prop, old, new ->
        println("$old -> new")
    ]
}

fun main(args: Array<String>){
    val user = User()
    user.name = "first"
    user.name = "second"
}
~~~

이 예는 다음을 출력합니다.

< no name > -> first

first -> second

만약 할당 과정 중간에 끼어들어 할당을 "거부(veto)" 하고 싶다면 observable() 대신에 vetoable() 을 사용하면 됩니다. 프로퍼티에 새 값을 할당하기 전에 vetoable 에 전달한 핸들러를 호출합니다.

### storing properties in a map

프로퍼티의 값을 맵에 저장하는 것은 일반적입니다. 이는 JSON 파싱이나 다른 "동적" 작업을 할 때 흔한 일입니다. 이 경우 위임 프로퍼티로 위임 객체 대신 맵 인스턴스 자체를 사용할 수 있습니다.

~~~kotlin
class User(val map: Map<String, Any?>){
    val name: String by map
    val age: Int by map
}
~~~

이 예제에서 생성자는 맵을 받습니다.

~~~kotlin
var user = User(mapOf(
	"name" to "John Doe",
	age to 25
))
~~~

위임 프로퍼티는 이 맵에서 값을 읽어옵니다.(문자열 키 - 프로퍼티의 이름)

~~~kotlin
println(user.name) // "John Doe" 출력
println(user.age) // 25 출력
~~~

읽기 전용 Map 대신에 MutableMap 을 사용하면 var 프로퍼티에도 동작합니다.

~~~kotlin
class MutableUser(val map: MutableMap<String, Any?>){
    var name: String by map
    var age: Int by map
}
~~~