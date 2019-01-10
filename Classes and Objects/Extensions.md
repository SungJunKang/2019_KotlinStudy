## 확장

C# 이나 Gosu 와 비슷하게 Kotlin 은 클래스를 확장하거나 데코레이터와 같은 디자인 패턴을 사용하지 않고 클래스에 새 기능을 확장하는 기능을 제공합니다. _ 확장(extension) _ 이라 불리는 특수한 선언을 이용해서 확장할 수 있습니다. Kotlin 은 _ 확장 함수 _ 와 _ 확장 프롵퍼티 _ 를 지원합니다.

### extension functions

확장 함수를 선언하려면 리시버 타입(receiver type)(확장할 타입)의 이름을 접두어로 사용해야 합니다. 다음은 MutableList< Int > 에 swap 함수를 추가합니다.

~~~kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int){
    val tmp = this[index1] // 'this'는 리스트에 해당합니다.
    this[index1] = this[index2]
    this[index2] = tmp
}
~~~

확장 함수에 this 키워드는 리시버 객체(점 기호 앞에 전달되는 객체)에 해당합니다. 이제 MutableList< Int > 에 대해 다음과 같이 함수를 호출할 수 있습니다.

~~~kotlin
val l = mutableListOf(1, 2, 3)

l.swap(0, 2) // 'swap()'에서 'this'는 'l'의 값을 갖습니다.
~~~

물론 이 함수는 모든 MutableList< T > 에 의미가 있으므로 제네릭으로 만들 수 있습니다.

~~~kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int){
    val tmp = this[index1] // 'this'는 리스트에 해당합니다.
    this[index1] = this[index2]
    this[index2] = tmp
}
~~~

함수 이름 앞에 리시버 타입 식에 사용가능한 타입 파라미터를 선언합니다.

### extensions are resolved statically

확장은 실제로 확장할 클래스를 수정하지 않습니다. 확장을 정의하면 클래스에 새 멤버를 추가하는 것이 아니라 단지 그 클래스의 인스턴스에 대해 점-기호로 호출할 수 있는 새로운 함수를 만드는 것입니다.

실행할 확장 함수는 **정적**으로 결정합니다. 예를 들어 리시버 타입에 따라 버추얼하게 결정하지 않습니다. 확장 함수를 호출하는 코드의 타입으로 호출할 확장 함수를 결정합니다. 런타임에 그 식을 평가한 결과 타입으로 결정하지 않습니다. 

~~~kotlin
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C){
    println(c.foo())
}

printFoo(D())
~~~

이 예는 "C" 를 출력합니다. 왜냐하면 호출할 확장 함수를 선택할 때 c 파라미터의 선언 타입인 C 클래스만 사용하기 때문입니다.

만약 클래스가 멤버 함수를 갖고 동일 리시버 타입을 갖는 동일 이름의 확장 함수가 있고 주어진 인자를 적용할 수 있다면 **항상 멤버가 이깁니다**. 

~~~kotlin
class C{
    fun foo(){
        println("member")
    }
}

fun C.foo(){
    println("extension")
}
~~~

### nullable receiver

Nullable 리시버 타입을 정의할 수도 있습니다. 이 확장은 비록 객체 변수가 null 이어도 호출되며 확장 함수 몸체에서 this == null 로 검사할 수 있습니다. 이것이 Kotlin 에서 null 검사 없이 toString() 을 호출할 수 있는 이유입니다. 확장 함수 안에서 null 여부를 검사합니다.

~~~kotlin
fun Any?.toString(): String{
    if(this == null){
        return "null"
    }
    // 검사 이후에 'this'를 non-null 타입으로 자동 변환하므로
    // 다음의 toString()은 Any 클래스의 멤버함수를 사용합니다.
    return toString()
}
~~~

### extension properties

함수와 유사하게 Kotlin 은 확장 프로퍼티를 지원합니다.

~~~kotlin
val <T> List<T>.lastIndex: Int
	get() = size - 1
~~~

확장은 실제로 클래스에 멤버를 추가하지 않으므로 확장 프로터리가 backing 필드를 가질 방법은 없습니다. 이것이 **확장 프로퍼티에 대해 initializer 를 허용하지 않는** 이유입니다. 프로퍼티 기능은 명시적으로 제공하는 getter/setter 로만 가능합니다.

예제 :

~~~kotlin
val Foo.bar = 1 // 에러 : 확장 프로퍼티에 대한 initializer 는 허용하지 않습니다.
~~~

### companion object extensions

클래스가 컴페니언 오브젝트를 컴페니언 오브젝트에 대해서 함수와 프로퍼티를 확장할 수 있습니다.

~~~kotlin
class MyClass{
    compainon object {} // "Companion"으로 불립니다.
}

fun MyClass.Compaion.foo(){
    // ...
}
~~~

컴페니언 오브젝트의 다른 멤버처럼 클래스 이름을 사용해야 확장을 호출할 수 있습니다.

~~~kotlin
MyClass.foo()
~~~

### scope of extensions

대부분 패키지 하위의 최상위 레벨에 직접 확장을 정의합니다.

~~~kotlin
package foo.bar

fun Baz.goo() { ... }
~~~

패키지 밖에서 이런 확장을 사용하려면 사용 측에서 확장을 임포트해야 합니다.

~~~kotlin
package com.example.usage

import foo.bar.goo // "goo"의 모든 확장을 임포트.
				   // 또는
import foo.bar.*   // "foo.bar"로부터 모두 임포트.

fun usage(baz: Baz){
    baz.goo()
}
~~~

### declaring extensions as members

클래스 안에서 다른 클래스를 위한 확장을 선언할 수 있습니다. 그 확장 안에는 리시버가 암묵적(implicit)으로(한정자(qualifier) 없이 접근할 수 있는 오브젝트 멤버) 다수 존재합니다. 확장을 선언한 클래스의 인스턴스를 _ 디스패치(dispatch) 리시버 _ 라 부르고, 확장 메서드의 리시버 타입 인스턴스를 _ 확장 리시버 _ 라고 부릅니다.

~~~kotlin
class D{
    fun bar() { ... }
}

class C{
    fun baz() { ... }
    
    fun D.foo(){
        bar() // D.bar 호출.
        baz() // C.bar 호출.
    }
    
    fun caller(d: D){
        d.foo() // 확장 함수 호출
    }
}
~~~

디스패치 리시버와 확장 리시버의 멤버 이름이 충돌하면 확장 리시버가 우선합니다. 디스패치 리시버의 멤버를 참조하려면 한정한 this 구문을 사용하면 됩니다.

~~~kotlin
class C{
    fun D.foo(){
        toString() // D.toString() 호출
        this@C.toString() // C.toString() 호출ㄹ
    }
}
~~~

멤버로 선언한 확장을 open 으로 설정하면 하위클래스에서 오버라이딩할 수 있습니다. 이는 확장 함수를 디스패치 리시버 타입에 따라 선택한다는 것을 의미합니다. 하지만 확장 리시버 타입은 정적입니다.

~~~kotlin
open class D{
    
}

class D1 : D(){
    
}

open class C{
    open fun D.foo(){
        println("D.foo in C")
    }
    
    open fun D1.foo(){
        println("D1.foo in C")
    }
    
    fun caller(d: D){
        d.foo() // 확장 함수 호출.
    }
}

class C1 :  C(){
    override fun D.foo(){
        println("D.foo in C1")
    }
    
    override fun D1.foo(){
        println("D1.foo in C1")
    }
}

C().caller(D()) // "D.foo in C" 출력.
C1().caller(D()) // "D.foo in C1" 출력 - 디스패치 리시버를 버추얼하게 선택.
C().caller(D1()) // "D.foo in C" 출력 - 확장 리시버를 정적으로 선택.
~~~

### motivation

Java 에서는 FileUtils, StringUtils 처럼 "Utills" 라는 이름을 갖는 클래스에 익숙합니다. 잘 알려진 java.util.Collections  도 이에 속합니다. 이런 유틸리티 클래스가 싫은 이유는 코드가 다음과 같은 모습을 띄기 때문입니다.

~~~kotlin
// Java
Collections.swap(list, Collections.binarySearch(list, Collections.max(otherList)),
                Collections.max(list))
~~~

a클래스 이름이 항상 방해가 됩니다. 정적 임포트를 사용하면 다음 코드가 됩니다.

~~~kotlin
// Java
swap(list, binarySearch(list, max(otherList)), max(list))
~~~

조금 나아졌지만 IDE 의 강력한 코드 완성 기능의 도움을 거의 받지 못합니다. 다음 코드처럼 할 수 있다면 훨씬 나을 것입니다.

~~~kotlin
// Java
list.swap(list.binarySearch(otherList.max()), list.max())
~~~

하지만 List 클래스에 모든 가능한 메서드를 구현하는 것은 원치 않습니다. 

























