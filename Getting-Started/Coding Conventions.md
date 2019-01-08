## 코드화 규칙

이 문서는 Kotlin을 위해 현재 사용 중인 코딩 스타일을 포함합니다.

- 소스 코드 구성
- 이름 지정 규칙
- 포맷
- 콜론
- 중복 구성 방지
- 언어 기능의 이상적 사용
- 라이브러리의 코드화 규칙

### Naming rules

애매하지 않다면, 다음 자바 코딩 규칙을 기본으로 사용합니다.

이름에는 낙타 표기법을 사용합니다.

~~~kotlin
open class DeclarationProcessor { ... }

object EmptyDeclarationProcessor : DeclarationProcessor() { ... }
~~~

#### function names

함수의 이름에서 프로퍼티와 지역 변수는 소문자로 시작하고 낙타 표기법을 사용하며 밑줄은 사용하지 않습니다.

~~~kotlin
fun processDeclarations() { ... }

var declarationCount = ...
~~~

예외로 클래스의 인스턴스를 만드는 데 사용되는 팩토리 함수는 작성 중인 클래스와 동일한 이름을 가질 수 있습니다.

~~~kotlin
abstract class Foo { ... }

class FooImpl : Foo { ... }

fun Foo(): Foo { return FooImpl(...) }
~~~

#### name for test methods

테스트에서는 backticks로 둘러싸인 공간이 있는 메서드 이름을 사용할 수 있습니다. 메서드 이름의 Underscores 역시 테스트 코드에서 허용됩니다.

~~~kotlin
class MyTestCase{
    @Test fun 'ensure everything works'() { ... }
    
    @Test fun ensureEventingWorks_onAndroid() { ... }
}
~~~

#### property names

상수의 이름(const로 표시된 프로퍼티 또는 불변하는 데이터를 포함하는 사용자 함수 get 기능이 없는 최상위 수준 또는 객체 값 프로퍼티)은 대문자로 구분된 이름을 사용해야 합니다.

~~~kotlin
const val MAX_COUNT = 8
val USER_NAME_FIELD = "UserName"
~~~

동작이 있는 오브젝트를 보관하는 최상위 수준이나 오브젝트 프로퍼티의 이름 또는 변할 수 있는 데이터는 일반적인 낙타 표기법을 사용해야 합니다.

~~~kotlin
val mutableCollection: MutableSet<String> = HashSet()
~~~

singleton 객체에 대한 참조를 포함하는 프로퍼티의 이름은 객체 선언과 동알힌 이름 지정 스타일을 사용할 수 있습니다.

~~~kotlin
val PersonComparator: Comparator<Person> = ...
~~~

#### names for backing properties

클래스가 개념적으로 동알히자민 하나는 공용 API의 일부이고 다른 하나는 구현 세부 정보인 경우, 프라이빗 프로퍼티의 접두사로 밑줄을 사용합니다.

~~~kotlin
class C{
    private val _elementList = mutableListof<Element>()
    
    val elementList: List<Element>
    	  get() = _elementList
}
~~~

### formatting

대부분의 경우, Kotlin은 Java 코드 규칙을 따릅니다.

들여쓰기에 공백 4개를 사용합니다.

~~~kotlin
if(elements != null){
    for(element in elements){
        // ...
    }
}
~~~

#### colon

다음의 경우 : 앞에 공백을 넣습니다.

- 타입과 슈퍼 타입을 분리하는 데 사용되는 경우.
- 슈퍼 클래스의 생성자나 동일 클래스의 다른 생성자에게 위임할 경우.
- 오브젝트 키워드 뒤.

선언문과 타입을 구분할 때 앞에 공백을 두지 않습니다.

항상 : 뒤에 공백을 둡니다.

~~~kotlin
abstract class Foo(out T : Any) : IFoo{
    abstract fun foo(a: Int): T
}

class FooImpl : Foo(){
	constructor(x: String) : this(x) { ... }
	
	val x = object : IFoo { ... }
}
~~~

### avoiding redundant constructs

#### unit

함수가 Unit을 리턴하면 리턴 타입을 생략합니다.

~~~kotlin
fun foo(){ // ": Unit"을 생략.
    
}
~~~

### idiomatic use of language features

#### lamda

람다식에서 중괄호 주변에 공백을 사용하고 파라미터와 몸체를 구분하는 -> 주변에도 공백을 사용합니다. 가능하면 람다를 괄호 밖에 전달합니다.

~~~kotlin
list.filter { it > 10 }.map { element -> element + 2 }
~~~

#### function vs properties

인자 없는 함수와 읽기 전용 프로퍼티는 서로 대신 사용할 수 있습니다. 의미가 비슷하긴 하지만 몇 가지 규칙에 따라 둘 중 하나를 선택합니다.

다음 알고리즘일 때 함수보다 프로퍼티를 선호합니다.

- 익셉션을 던지지 않을 때.
- 0(1) 복잡도를 가질 때.
- 계산 비용이 작을 때.
- 호출과 같은 결과를 리턴할 때.











