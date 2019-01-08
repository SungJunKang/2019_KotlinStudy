## 클래스와 상속

### class

Kotlin 은 class 키워드를 사용해서 클래스를 선언합니다.

~~~kotlin
class Invoice{
    
}
~~~

클래스 선언은 클래스 이름, 클래스 헤더(타입 파라미터 지정, 주요 생성사 등)와 중괄호로 둘러 싼 클래스 몸체로 구성됩니다. 헤더와 몸체는 필수가 아닙니다. 클래스가 몸체를 갖지 않으면 중괄호를 생략할 수 있습니다.

~~~kotlin
class Empty
~~~

#### constructors

Kotlin 클래스는 한 개의 **주요(primary) 생성자**와 한 개 이상의 **보조(secondary) 생성자**를 가질 수 있습니다. 주요 생성자는 클래스 헤더에 속합니다. 클래스 헤더는 클래스 이름 뒤에(타입 파라미터가 있다면 그 뒤에) 위치합니다.

~~~kotlin
class Person constructor(firstName: String){
    
}
~~~

주요 생성자가 어노테이션이나 가시성 제한자를 갖지 않으면 constructor 키워드를 생략할 수 있습니다.

~~~kotlin
class Person(firstName: String){
    
}
~~~

주요 생성자는 어떤 코드도 포함할 수 없습니다. 초기화 코드는 init 키워드를 이용한 **initializer 블록**에 위치할 수 있습니다.

~~~kotlin
class Customer(name: String){
    init{
        logger.info("Customer initialized with value ${name}")
    }
}
~~~

주요 생성자의 파라미터는 initializer 블록과 몸체에 선언한 프로퍼티 initializer 에서 사용할 수 있습니다.

~~~kotlin
class Customer(name: String){
    val customerKey = name.toUpperCase()
}
~~~

Kotlin 은 주요 생성자에 프로퍼티를 선언하고 초기화할 수 있는 간결한 구문을 제공합니다.

~~~kotlin
class Person(val firstName: String, val lastName: String, var age: Int){
    // ...
}
~~~

일반 프로퍼티와 동일하게 주요 생성자에 선언한 프로퍼티도 변경 가능(var)하거나 읽기 전용(val)일 수 있습니다.

생성자가 어노테이션이나 가시성 제한자를 가질 경우 constructor 키워드가 필요하며 키워드 앞에 제한자가 위치하게 됩니다.

~~~kotlin
class Customer public @Inject constructor(name: String){
    // ...
}
~~~

클래스는 constructor를 이용해서 **보조 생성자**를 선언할 수 있습니다.

~~~kotlin
class Person{
    constructor(parent: Person){
        parent.children.add(this)
    }
}
~~~

클래스가 주요 생성자를 가질 경우, 각 보조 생성자는 직접 주요 생성자에게 위임하거나 다른 보조 생성자를 통해 간접적으로 주요 생성자에 위임해야 합니다. this 키워드를 이용해서 같은 클래스의 다른 생성자에게 위임할 수 있습니다.

~~~kotlin
class Person(val name: String){
    constructor(name: String, parent: Person) : this(name){
        parent.children.add(this)
    }
}
~~~

추상이 아닌 클래스가 어떤 생성자도 선언하지 않으면 인자를 갖지 않은 주요 생성자를 만듭니다. 클래스가 공개(public) 생성자를 갖지 않기를 원하면 기본 가시성이 아닌 다른 가시성을 갖는 빈(empty) 주요 생성자를 추가해야 합니다.

~~~kotlin
class DontCreateMe private constructor(){
    
}
/*
주의 : JVM에서 주요 생성자의 모든 파라미터가 기본 값을 가지면 컴파일러는 기본 값을 사용하는 파라미터 없는 생성자를 추가로 만듭니다. 이는 Jackson 이나 JPA 처럼 파라미터 없는 생성자를 이용해서 클래스 인스턴스를 생성하는 라이브러리에 대해 Kotlin 을 사용하게 쉽게 만들어줍니다.
*/

class Customer(val customerName: String = "")
~~~

#### creating instances of classes

클래스의 인스턴스를 생성하려면 일반 함수와 비슷하게 생성자를 호출합니다.

~~~kotlin
val invoice = Invoice()

val customer = Customer("Joe Smith")
~~~

Kotlin 은 new 가 없음에 유의합니다.

#### class members

클래스는 다음을 갖습니다.

- 생성자와 initializer 블록
- 함수
- 프로퍼티
- 중첩 클래스와 내부 클래서
- 오브젝트 선언

### inheritance

Kotlin 의 모큰 클래스는 공통의 상위 클래스(superclass)인 Any 를 갖습니다. Any 는 상위타입(supertype)을 지정하지 않은 클래스의 기본 상위 타입입니다.

~~~kotlin
class Example // 기본으로 Any 를 상속합니다.
~~~

Any 는 java.lang.Object 가 아닙니다. 특히 equals(), hashCode(), toString() 외에 다른 멤버를 갖지 않습니다.

상위타입을 직접 선언하려면 클래스 헤더에 콜론 뒤에 타입을 위치시킵니다.

~~~kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
~~~

클래스가 주요 생성자를 가지면 주요 생성자의 파라미터를 이용해서 베이스 타입을 바로 초기화할 수 있습니다.(그리고 반드시 초기화해야 합니다.)

클래스가 주요 생성자를 갖지 않으면 각 보조 생성자는 super 키워드를 사용해서 베이스 타입을 초기화하거나 그것을 하는 다른 생성자에 위임해야 합니다. 위임받은 다른 보조 생성자는 베이스 타입의 다른 생성자를 호출할 수 있습니다.

~~~kotlin
class MyView : View{
    constructor(ctx: Context) : super(ctx)
    
    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
~~~

클래스의 open 어노테이션은 Java 의 final 과 정반대입니다. open 은 다른 클래스가 이 클래스를 상속할 수 있도록 합니다.  기본적으로 Kotlin 에서 모든 클래스는 final 입니다.

#### overriding methods

앞서 언급한 것처럼 Kotlin 에서는 뭐든 명시적으로 만드는 것을 고수합니다. Java 와 달리 Kotlin 은 오버라이딩 가능한 멤버와 오버라이드를 위해 명시적으로 어노테이션을 붙여야 합니다.

~~~kotlin
open class Base{
    open fun v(){ ... }
    fun nv(){ ... }
}

class Derived() : Base(){
    override fun v(){ ... }
}
~~~

Derived.v() 는 override 어노테이션이 필요합니다. 붙이지 않으면 컴파일 에러가 발생합니다. Base.nv() 와 같이 함수에 open 을 붙이지 않으면 override 여부에 상관없이 하위클래스에서 동일 시그니처를 갖는 메서드를 선언할 수 없습니다. open 어노테이션이 없는 final 클래스에는 open 멤버가 금지됩니다.

override 를 갖는 멤버는 그 자체로 open 이며 하위클래스에서 오버라이딩할 수 있습니다. 오버라이딩을 막고 싶다면 final 을 사용하면 됩니다.

~~~kotlin
open class AnotherDerived() : Base(){
    final override fun v() [ ... ]
}
~~~

#### overriding rules

Kotlin 에서 구현 상속은 다음 규칙을 따릅니다. 클래스가 바로 상위의 여러 상위클래스에서 같은 멤버 구현을 상속하면, 반드시 이 멤버를 오버라이딩하고 자신의 구현을 제공해야 합니다. (대게 상속 받은 것 중의 하나를 사용하는 구현을 제공.) 사용할 상위타입의 구현을 지정하려면 화살괄호에 상위타입 이름을 지정한 super 를 사용합니다.

~~~kotlin
open class A{
    open fun f(){
        print("A")
    }
    fun a(){
        print("a")
    }
}

interface B{
    fun f(){
        print("B") // 인터페이스의 멤버는 기본적으로 'open' 입니다.
    }
    fun b(){
        print("b")
    }
}

class C() : A(), B{
    // 컴파일하라면 f()를 오버라이딩해야 합니다.
    override fun f(){
        super<A>.f() // call to A.f()
        super<B>.f() // call to B.f()
    }
}
~~~

A 와 B 를 상속받는 것은 괜찮으며.  a() 와 b() 에는 상속 관련 문제가 없습니다. 왜냐하면 C 는 이 두 함수에 대해 각각 한 개의 구현만 상속 받기 때문입니다. 하지만 f() 의 경우 C 가 두 개의 구현을 상속바다기 때문에 모호함을 제거하기 위해 C 에 f() 구현을 제공해야 합니다.

### abstract classes

클래스와 멤버를 abstract 로 선언할 수 있습니다. 추상 멤버는 구현을 갖지 않습니다. 추상 클래스나 함수는 open 을 붙일 필요가 없습니다.

추상이 아닌 open 멤버를 추상 멤버로 오버라이딩할 수 있습니다.

~~~kotlin
open class Base{
    open fun f(){ ... }
}

abstract class Derived : Base(){
    override abstract fun f()
}
~~~

### companion objects

Java 나 C# 과 달리 Kotlin 의 클래스는 정적 메서드를 갖지 않습니다. 많은 경우 그 대신 패키지 수준의 함수를 사용할 것을 권합니다.

만약 클래스 인스턴스 없이 클래스의 내부에 접근해야하는 함수를 작성하고 싶다면(예: 팩토리 메서드) 그 클래스 안에 선언한 오브젝트의 멤버로 함수를 작성할 수 있습니다.

좀 더 구체적으로 말하면 클래스 안에 컴페니언 오브젝트를 선언하면 클래스 이름만으로 Java/C# 의 정적 메서드를 호출하는 것처럼 컴페니언 오브젝트의 멤버를 호출할 수 있습니다.















