## 인터페이스

Kotlin 인터페이스는 Java 8 과 매우 유사합니다. 추상 메서드뿐만 아니라 메서드 구현을 가질 수 있습니닫. 추상 클래스와의 차이점은 인터페이스는 상태를 가질 수 없다는 점입니다. 프로퍼티를 가질 수는 있지만 추상이거나 accessor 구현을 제공해야 합니다.

interface 키워드로 인터페이스를 정의합니다.

~~~kotlin
interface MyInterface{
    fun bar()
    fun foo(){
        // 몸체 선택
    }
}
~~~

### implementing interfaces

클래스나 오브젝트는 한 개 이상의 인터페이스를 구현할 수 있습니다.

~~~kotlin
class Child : MyInterface{
    override fun bar(){
        // 몸체
    }
}
~~~

### properties in interfaces

인터페이스에 프로퍼티를 선언할 수 있습니다. 인터페이스에 선언한 프로퍼티는 추상이거나 accessor 를 위한 구현을 제공해야 합니다. 인터페이스의 프로퍼티는 backing 필드를 가질 수 없으므로 인터페이스에 선언한 accessor 는 backing 필드를 참조할 수 없습니다.

~~~kotlin
interface MyInterface{
    val property: Int // 추상
    
    val propertyWithImpleementation: String
    	get() = "foo"
    	
    fun foo(){
        print(property)
    }
}

class Child : MyInterface{
    override val property: Int = 29
}
~~~

### resolving overriding conflicts

 상위타입 목록에 여러 타입을 지정하면 같은 메서드에 대해 한 개 이상의 구현을 상속받기도 합니다. 

~~~kotlin
interface A{
    fun foo(){
        print("A")
    }
    fun bar()
}

interface B{
    fun foo(){
        print("B")
    }
    fun bar(){
        print("bar")
    }
}

class C : A{
    override fun bar(){
        print("bar")
    }
}

class D : A, B{
    override fun foo(){
        super<A>.foo()
        super<B>.foo()
    }
    
    override fun bar(){
        super<B>.bar()
    }
}
~~~

A 와 B 인터페이스가 foo()와 bar() 함수를 선언하고 있습니다. foo()는 두 인터페이스가 모두 구현하고 있고 bar()는 B 만 구현하고 있습니다. (A 에서 bar()를 abstract 로 지정하지 않았는데 그 이유는 인터페이스에서는 함수에 몸체가 없으면 기본으로 추상이기 때문입니다.) 컨크리트 클래스  C 가 A 를 상속받으면 C 는 명백하게 bar()를 오버라이딩해서 구현을 제공해야 합니다. 그리고 A 와 B 를 상속받은 D 는 bar()를 오버라이딩할 필요가 없습니다. 왜냐면 한 개 구현만 상속 받기 때문입니다. 하지만 foo()는 두 개 구현을 상속 받기 때문에 컴파일러는 둘 중 무엇을 선택해야 하는지 알 수 없습니다. 따라서 컴파일러는 foo()를 명시적으로 오버라이딩할 것을 강제합니다.