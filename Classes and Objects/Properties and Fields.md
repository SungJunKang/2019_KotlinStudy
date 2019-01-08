## 프로퍼티와 필드

### declaring properties

Kotlin 클래스는 프로퍼티를 가질 수 있습니다. var 키워드로 변경 가능 프로퍼티를 선언하고 val 키워드로 읽기 전용 프로퍼티를 선언합니다.

~~~kotlin
public class Adress{
    public var name: String = ...
    public var street: String = ...
    public var city: String = ...
    public var state: String? = ...
    public var zip: String = ...
}
~~~

프로퍼티를 사용하려면 Java 필드처럼 단순히 이름으로 참조하면 됩니다.

~~~kotlin
fun copyAddress(address: Address): Address{
    val result = Address()
    
    result.name = address.name // accessor 실행.
    result.street = address.street
    // ...
    
    return result
}
~~~

### getter and setter

프로퍼티 선언의 전체 구문은 다음과 같습니다.

~~~kotlin
var <propertyName>: <PropertyType> [= <property_intializer>]
	[<getter>]
	[<setter>]
~~~

initializer, getter, setter 은 선택 사항입니다. initalizer 나 오버라이딩한 베이스 클래스 멤버에서 타입을 유추할 수 있다면 프로퍼티 타입을 생략해도 됩니다.

예 :

~~~kotlin
var allByDefault: Int? // 에러: initializer 필요, 기본 getter 와 setter 포함.
var initialized = 1 // Int 타입, 기본 getter 와 setter 가짐
~~~

읽기 전용 프로퍼티 선언의 전체 구문은 변경 가능 프로퍼티와 두 가지가 다릅니다. var 대신에 val 로 시작하고 setter 를 허용하지 않습니다.

~~~kotlin
val simple: Int? // Int 타입과 기본 getter 을 갖고, 생성자에서 초기화해야 합니다.
val inferredType = 1 // Int 타입과 기본 getter 를 갖습니다.
~~~

프로퍼티 선언에서 바로 뒤에 일반 함수와 유사한 커스텀 accessor 를 작성할 수 있습니다. 다음은 커스텀 getter 의 예입니다.

~~~kotlin
val isEmpty: Boolean
	get() = this.size == 0
~~~

다음은 커스텀 setter 의 예입니다.

~~~kotlin
var stringRepresentation: String
	get() = this.toString()
	set(value){
        setDataFromString(value) // 문자열을 파싱해서 다른 프로퍼티에 할당합니다.
	}
}
~~~

보통 setter 의 파라미터 이름으로 value 를 사용하지만 원하는 다른 이름을 사용해도 됩니다.

accessor 의 기본 구현을 변경하지 않고 가시성을 변경하거나 어노테이션을 적용하고 싶다면 몸체 선언 없이 accessor 를 정의할 수 있습니다.

~~~kotlin
var setterVisibility: String = "abc
	private set // setter 는 private 이고 기본 구현을 갖습니다.

var setterWithAnnotation: Any? = null
	@Inject set // setter 는 @Inject 를 붙입니다.
~~~

#### backing fields

Kotlin 클래스는 필드를 가질 수 없습니다. 하지만 커스텀 accessor 를 사용하면 backing 필드가 필요할 때가 있습니다. 이를 위해 Kotlin 은 field 식별자를 사용해서 접근할 수 있는 backing 필드를 제공합니다.

~~~kotlin
var counter = 0 // 초기화 값을 backing 필드에 직접 씁니다.
	set(value){
        if(value >= 0){
            field = value
        }
	}
~~~

field 식별자는 프로퍼티 accessor 에서만 사용할 수 있습니다.

accessor 몸체에서 backing 필드를 사용하면(또는 기본 구현을 사용하면) 컴파일러는 backing 필드를 생성합니다. 그렇지 않으면 생성하지 않습니다.

예를 들어 다음의 경우에는 backing 필드가 없습니다.

~~~kotlin
val isEmpty: Boolean
	get() = this.size == 0
~~~

#### backing properties

"기본(implicit) backing 필드" 방식과 맞지 않는 것을 하고 싶다면 backing 프로퍼티로 대체할 수 있습니다.

~~~kotlin
private var _table: Map<String, Int>? = null
public val table: Map<String, Int>
	get(){
        if(_table == null){
            _table = HashMap() // 타입 파라미터를 유추.
        }
        return _table ?: throw AssertionError("Set to null another thread")
	}
~~~

기본 getter 와 setter 로 private 프로퍼티에 접근하는 것을 최적화해서 함수 호출에 따른 오버헤드가 없기 때문에 모든 점에서 Java 와 같습니다.

### compile-time constants

컴파일 타임에 값을 알 수 있는 프로퍼티는 const 제한자를 이용해서  _ 컴파일 타임 상수 _로 지정할 수 있습니다. 이 프로퍼티는 다음 요건을 충족해야 합니다.

- 최상위 레벨 또는 Object 의 멤버.
- String 이나 기본 타임 값으로 초기화.
- 커스텀 getter 없음.

이 프로퍼티는 어노테이션에서 사용할 수 있습니다.

~~~kotlin
const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

@Deprecated(SUBSYSTEM_DEPRECATED) fun foo() { ... }
~~~

### late-initialized properites and variables

보통 non-null 타입을 갖는 프로퍼티를 선언하면 생성자에서 초기화해야 합니다 하지만 이게 늘 편한 것은 아닙니다. 예를 들어 의존 주입이나 단위 테스트의 셋업 메서드에서 프로퍼티를 초기화할 수 있습니다. 이 경우 생성자에 non-null initializer를 제공할 수 없지만 클래스 몸체 안에서 프로퍼티를 참조할 때 null 검사를 하고 싶지는 않을 것입니다.

이 경우를 처리하기 위해 프로퍼티를 lateinit 로 제한할 수 있습니다.

~~~kotlin
public class MyTest{
    lateinit var subject: TestSubject
    
    @SetUp fun setup(){
        subject = TestSubject()
    }
    
    @Test fun test(){
        subject.method() // null 검사 없이 직접 접근.
    }
}
~~~

이 제한자는 주요 생성자가 아닌 클래스 몸체에 선언되고 커스텀 getter 나 setter 를 갖지 않는 var 프로퍼티에만 사용할 수 있습니다. 프로퍼티 타입은 non-null 이어야 하고 기본 타입이면 안 됩니다.

초기화되기 전에 lateinit 프로퍼티에 접근하면 초기화되지 않은 프로퍼티에 접근했음을 정확하게 알려주기 위해 특수한 익셉션을 발생합니다.

### deleegated properties

대부분 프로퍼티는 단순히 backing 필드에서 값을 읽거나 씁니다. 반면에 커스텀 getter 와 setter 를 갖는 프로퍼티는 프로퍼티의 행위를 구현할 수 있습니다. 프로퍼티 행위 구현에는 프로퍼티가 어떻게 작동하는지에 대한 어떤 공통 패턴이 있습니다. 키로 맵에서 읽기, 데이터베이스 접근하기, 접근 시 리스너에 통지하기 등이 공통 패턴에 해당합니다.





