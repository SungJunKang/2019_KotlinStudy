## 제네릭

Java 처럼 Kotlin 클래스도 타입 파라미터를 가질 수 있습니다.

~~~kotlin
class Box<T>(t: T){
    var value = t
}
~~~

이 클래스의 인스턴스를 생성하려면 타입 인자를 제공해야 합니다.

~~~kotlin
val box: Box<Int> = Box<Int>(1)
~~~

생성자 인자나 다른 방법으로 파라미터를 유추할 수 있으면 타입 인자를 생략할 수 있습니다.

~~~kotlin
val box = Box(1) // 1은 Int 타입을 가지므로 컴파일러는 Box<Int> 라고 알아냅니다.
~~~

### variance

Java 타입 시스템에서 가장 복잡한 부분 중 하나가 와일드카드 타입입니다. Kotlin 에는 복잡한 와일드 카드가 없습니다. 대신 선언-위치 가변(declaration-site variance) 과 타입 프로젝션(type projection) 의 두 가지를 제공합니다.

먼저 Java 에서 미스테리한 와일드카드가 필요한 이유를 생각해봅시다. 첫째, Java 의 제네릭 타입은 **무공변(invariant)** 입니다. 이는 List < String > 은 List < Object > 의 하위타입이 **아님**을 의미합니다. 만약 리스트가 **무공변(invariant)**이 아니면 Java 배열보다 나을 것이 없습니다. 왜냐하면 다음 코드가 컴파일은 되지만 런타임에서 익셉션이 발생하기 때문입니다.

~~~java
// Java
List<String> strs = new ArrayList<String>();
List<Object> objs = strs; // !!! 앞에서 언급한 문제 원인이 여기 있습니다. Java 는 이를 금지합니다.
objs.add(1); // 여기서 String 의 리스트에 Integer 를 넣습니다.
String s = strs.get(0); // !!! ClassCastException : Integer 를 String 으로 변환 할 수 없습니다.
~~~

그래서 Java 는 런타임 안정성을 보장하기 위해 이런 것을 금지했습니다. 하지만 이는 몇 가지의 영향을 줍니다. 예를 들어 Collection 인터페이스의 addAll() 메서드를 생각해봅시다. 이 메서드의 시그니처는 무엇일까요? 직관적으로 생각하면 다음과 같이 작성할 수 있습니다.

~~~java
// Java
interface Collection<E> ... {
    void addAll(Collection<E> items);
}
~~~

하지만 완벽하게 안전한 코드임에도 다음의 간단한 코드를 만들 수 없습니다.

~~~java
// Java
void copyAll(Collection<Object> to, Collection<String> from){
    to.addAll(from); // !!! addAll 의 단순한 선언으로는 컴파일되지 않습니다.
    				 // Collection<String> 은 Collection<Object> 의 하위타입이 아닙니다.
}
~~~

이런 이유로 실제 addAll() 시그니처는 다음과 같습니다.

~~~java
// Java
interface Collection<E> ... {
    void addAll(Collectcin<? extends E> items);
}
~~~

**와일드카드 타입 인자** ? extends E 는 이 메서드가 E 가 아닌 E 의 하위타입의 객체 컬렉션은 허용한다는 것을 말합니다. 이는 items 에서 안전하게 E 로 **읽을** 수 있지만(이 컬렉션의 요소는 E 의 하위클래스의 인스턴스입니다.), E 의 어떤 하위타입인지 모르기 때문에 items 에 **쓸 수 없다**는 것을 의미하바니다. 이런 제한을 해소하기 위해 Collection < String > 이 Collection < ? extends Object > 의 하위타입이 되도록 기능을 추가했습니다. "전문 용어" 로 **extends**-bound(**upper** bound) 를 갖는 와일드카드를 사용해서 타입을 **공변(convariant)**으로 만들었습니다.

이 트릭이 왜 작동하는지 이해하는데 있어 핵심은 다소 단순합니다. 만약 컬렉션에서 아이템을 **가져올** 수만 있다면 String 컬렉션에서 Object 를 읽는 것은 괜찮습니다. 역으로 컬렉션에 항목을 넣을 수만 있다면 Object 컬렉션에 String 을 넣는 건 괜찮습니다. Java 에서 List < ? Super String > 가 List< Object > 의 **상위타입**이 됩니다.

후자를 **반공변(contravariance)**이라 부르며, List < ? super String > 에 인자로 String 을 받는 메서드만 호출할 수 있습니다.(예를 들어 add(String) 이나 set(int, String) 을 호출할 수 있습니다.) 반면에 List<T> 에서 T 를 리턴하는 어떤 것을 호출하면 String 이 아닌 Object 를 얻게 됩니다.

Joshua Blochs 는 이 객체는 **Producer**에서만 **읽을** 수 있고 **Consumer**로만 **쓸** 수 있다고 했습니다. Joshua Blochs 는 "유연함을 최대한 얻으려면 producer 나 consumer 를 표현하는 입력 파라미터에 와일드카드 타입을 사용하라" 고 권하고 있으며, 다음과 같이 기억을 쉽게 할 수 있는 약자를 제시했습니다.

PECS 는 Producer-Extends, Consumer-Super 를 의미합니다.

주의 : producer 객체를 사용한다면, 예를 들어 List < ? Extends Foo >, 이 객체에 대해 add() 나 set() 을 호출하는 것을 허용하지 않습니다. 하지만 이것이 이 객체가 **불변(immutable)**인 것을 의미하는 것은 아닙니다. 예를 들어, 리스트의 모든 항목을 삭제하기 위해 clear() 를 호출하는 것은 가능합니다. 왜냐하면, clear() 는 어떤 파라미터도 갖지 않기 때문입니다. 와일드카드(또는 다른 종류의 가변(variance)) 가 보장하는 것은 **타입 안정성**입니다. 불변은 완전히 다른 얘기입니다.

#### declaration-site variance

Source < T > 제네릭 인터페이스에 T 를 파라미터로 갖는 메서드는 없고 단지 T 를  리턴하는 메서드만 있다고 해봅니다.

~~~java
// Java
interface Source<T>{
	T nextT();
}
~~~

이때 Source < Object > 타입 변수에 Source < String > 인스턴스를 할당하는 것은 완벽히 안전한 것입니다. 여기엔 어떤 consumer 메서드도 호출하지 않습니다. 하지만 Java 는 이를 알지 못하기 때문에 이를 금지합니다.

~~~java
// Java
void demo(Source<String> strs){
    Source<Object> objects = strs; // !! Java 는 허용하지 않음.
    // ...
}
~~~

이 코드의 문제점을 고치려먼 Source < ? extends Object> 타입 객체를 선언해야 하는데 이는 다소 의미가 없습니다. 왜냐하면 수정 전에도 동일한 메서드를 호출할 수 있고 더 복잡한 타입으로 수정해도 값을 추가하지 않기 때문입니다. 하지만 컴파일러는 이를 알지 못합니다.

Kotlin 은 컴파일러에 이런 류의 내용을 설명하는 방법이 존재합니다. 이를 **선언-위치 가변(declaration-site variance)** 이라고 부릅니다. 소스 코드의 **타입 파라미터** T 에  어노테이션을 붙여서 Source < T > 의 멤버가 **리턴**(생성)만 하고 소비하지 않는다고 할 수 있습니다. 이를 위해 **out** 제한자를 제공합니다.

~~~kotlin
abstract class Source<out T>{
    abstract fun nextT(): T
}

fun demo(strs: Source<String>){
    val objects: Source<Any> = strs // T 는 out 파라미터이므로 OK.
    // ...
}
~~~

일반 규칙 : 클래스 C의 타입 파라미터 T 를 **out** 으로 선언하면 타입 파라미터는 오직 C 멤버의 **out**-위치에만 올 수 있습니다. 하지만 리턴에서 C < Base > 는 안전하게 C < Derived > 의 상위타입이 될 수 있습니다.

"전문 용어" 로 클래스 C 는 파라미터 T 에 공변(covariant) 한다 또는 T 는 공변(covariant) 타입 파라미터라고 말합니다. C 를 T 의 **consumer** 가 아닌 T 의 **producer** 라고 생각할 수 있습니다.

**out** 제한자는 **가변(variance) 어노테이션**이라 부르며, 타입 파라미터 선언 위치에 제공하기 때문에 **선언-위치 가변(declaration-site variance)** 에 대한 것입니다. 이는 Java 가 타입을 사용할 때 와일드카드로 타입을 공변(covariant) 하게 만드는 **사용-위치 가변(use-site variance)** 인 것과 다릅니다.

**out** 과 더불어 Kotlin 은 대체 가변(variance) 어노테이션인 **in** 을 제공합니다. in 은 파라미터를 반공변(contravariant) 으로 만들어 줍니다. 이는 오직 consume 만 될 수 있으며, produce 할 수 없습니다. 반공변(contravariant) 클래스의 좋은 예가 Comparable 입니다.

~~~kotlin
abstract class Comparable<in T>{
    abstract fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>){
    x.compareTo(1.0) // 1.0 은 Number 의 상위 타입인 Double 타입을 가집니다.
    // 그래서, COmparable<Double> 타입 변수를 x 에 할당할 수 있습니다.
    val y: Comparalbe<Double> = x // OK !
}
~~~

단어 **in** 과 **out** 은 자명하므로(이미 꽤 오랜 시간 C# 에서 성공적으로 사용하고 있습니다.) 위에서 언급한 기억하기 위한 PECS 가 실제로 필요 없고 더 상위 목표를 위해 바뀔 수 있다고 생각합니다.

### type projections

#### Use-site variance: Type projections

타입 파라미터 T 를 out 으로 선언하는 것은 매우 편리하고 사용 위치에서 하위타입 관련 문제가 없습니다. 그런데 클래스가 실제로 T 만 리턴하도록 제한이 있을 때 그것으로 못하는 것은 무엇일까요? Array 가 좋은 예입니다.

~~~kotlin
class Array<T>(val size: Int){
    fun get(index: Int): T { /* ... */ }
    fun set(index: Int, value: T){ /* ... */ }
}
~~~

이 클래스는 T 에 대해 공변(covariant)도 반공변(contravariant)도 될 수 없습니다. 게다가 유연하지도 못하게 강제합니다. 

~~~kotlin
fun copy(from: Array<Any>, to: Array<Any>){
    assert(from.size == to.size)
    for(i in from.indices){
        to[i] = from[i]
    }
}
~~~

이 함수는 한 배열에서 다른 배열로 항목을 복사합니다. 실제로 함수 실행을 시도해봅시다. 

~~~kotlin
val ints: Array<Int> = arrayOf(1, 2, 3)
val any = Array<Any>(3)

copy(ints, any) // 에러 : expects (Array<Any>, Array<Any>)
~~~

여기서 익숙한 문제가 발생합니다. Array < T > 는 T 에 대해 **무공변(invariant)**하므로 Array < Int > 와 Array < Any > 는 서로 상대방의 하위타입이 아닙니다. 왜 그럴까요? copy 는 나쁜 짓을 **할 지 모르기** 떄문입니다. 예를 들어, String 을 from 에 **쓰려고** 시도하는데 실제로 from 에 Int 배열을 전달했다면 나중에 ClassCastException 이 발생할 수 있습니다.

여기서 원하는 것은 copy() 가 그런 나쁜 짓을 하지 않는 것을 보장하는 것입니다. 이 메서드가 from 에 **쓰지** 못하게 하려면 다음과 같이 하면 됩니다.

~~~kotlin
fun copy(from: Array<out Any>, to: Array<Any>){
    // ...
}
~~~

여기서 사용한 것은 **타입 프로젝션(type projection)** 이라고 합니다. 이 코드에서 from 은 단순 배열이 아닌 **제한된(projected)** 배열입니다. 오직 파라미터 T 를 리턴하는 메서드만 호출할 수 있습니다. 이 예의 경우 get() 만 호출할 수 있습니다. 이것이 Kotlin 의 **사용-위치 가변(use-site variance)** 접근 방식입니다. Java 의 Array < ? extends Object > 에 해당하지만 더 간단합니다.

**in** 을 이용해서 타입을 프로젝션할 수 있습니다.

~~~kotlin
fun fill(dest: Array<in String>, value: String){
    // ...
}
~~~

Array < in String > 은 Java 의 Array < ? super String > 에 해당하며  CharSequence 의 배열이나 Object 의 배열을 fill() 함수에 전달할 수 있습니다.

### star-projections

때때로 타입 인자에 대해 알지 못하지만 안전한 방법으로 인자를 사용하고 싶을 때가 있습니다. 여기서 안전한 방법은 제네릭 타입에 그런 프로젝션을 정의해서 제네릭 타입의 모든 컨크리트 인스턴스가 그 프로젝트의 하위 타입이 되도록 하는 것입니다.

Kotlin 은 이를 위해 **스타 프로젝션(star-projection)** 이라 불리는 구문을 제공합니다.

- Foo < out T > 에 대해, T 가 upper bound TUpper 를 갖는 공변(convariant) 타입 파라미터면 Foo < * > 은 Foo < out TUpper > 와 같습니다. T 를 몰라도 안전하게 Foo < * > 에 TUpper 값을 읽을 수 있다는 것을 의미합니다.
- Foo < in T > 에 대해, T 가 반공변(contravariant) 타입 파라미터면 Foo < * > 는 Foo < in Nothing > 와 같습니다. 이는 T 를 모를 때 안전하게 Foo < * > 에 쓸 수 없다는 것을 의미합니다.
- Foo < T > 에 대해, T 가 upper bound TUpper 를 갖는 무공변(invariant) 타입 파라미터면, Foo < * > 는 값을 읽을 때는 Foo < out TUpper > 와 동일하고 값을 쓸 때는 Foo < in Nothing > 와 동일합니다.

제네릭 타입이 여러 타입의 파라미터를 가질 경우 각각 독립적으로  프로젝션할 수 있습니다. 예를 들어 interface Function < in T, out U > 타입을 정의하면 다음의 스타-프로젝션을 생각할 수 있습니다.

- Function < *, String > 은 Function < in Nothing, String > 을 의미합니다.
- Function < Int, * > 은 Function < Int, out Any? > 를 의미합니다.
- Function < *, * > 은  Function < in Nothing, out Any? > 을 의미합니다.

주의 : 스타-프로젝션은 Java 의 raw 타입과 매우 유사하지만 안전합니다.

### generic functions

클래스만 타입 파라미터를 가질 수 있는 것은 아닙니다. 함수도 가질 수 있습니다. 함수 이름 앞에 타입 파라미터를 위치시키면 됩니다.

~~~kotlin
fun <T> singletonList(item: T): List<T>{
    // ...
}

fun <T> T.basicToString() : String{ // 확장 함수
    // ...
}
~~~

타입 파라미터를 호출 위치(call site)에서 명시적으로 전달하려면 함수 이름 **뒤에** 지정합니다.

~~~kotlin
val l = singletonList<Int>(1)
~~~

### generic constraints

주어진 타입 파라미터를 대체할 수 있는 모든 가능한 타입은 **제네릭 계약(generic constraints)**에 따라 제한됩니다.

#### upper bounds

가장 일반적인 제약은 Java 의 extends 키워드에 해당하는 **upper bound** 입니다.

~~~kotlin
fun <T : Comparable<T>> sort(list: List<T>){
    // ...
}
~~~

콜론 뒤에 지정한 타입이 **upper bound** 입니다. Comparable < T > 의 하위타입만 T 를 대체할 수 있습니다. 

~~~kotlin
sort(listOf(1, 2, 3)) // OK! Int 는 Comparable<Int> 의 하위타입입니다.
sort(listOf(HashMap<Int, String>())) // 에러 : HashMap<Int, String> 은 Comparable<HashMap<Int, String> 의 하위타입이 아닙니다.
~~~

upper bound 를 지정하지 않을 경우 기본 upper bound 는 Any? 입니다. 화살괄호 안에 오직 한 개의 upper bound 만 지정할 수 있습니다. 동일 타입 파라미터에 대해 한 개 이상의 upper bound 가 필요하면 별도의 **where**-절을 사용해야 합니다.

~~~kotlin
fun <T> cloneWhenGreater(list: List<T>, threshold: T): List<T>
	where T: Comparable,
		  T: Cloneable{
    return list.filter { it > threshold }.map { it.clone() }
}
~~~



















