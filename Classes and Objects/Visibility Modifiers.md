##  가시성 제한자

클래스, 오브젝트, 인터페이스, 생성자, 함수, 프로퍼티와 프로퍼티의 setter 는 _ 가시성 제한자 _를 가질 수 있습니다. (getter 는 항상 프로퍼티와 동일한 가시성을 갖습니다.) Kotlin 에는 private, protected, internal, public 의 네 개의 가시성 제한자가 존재합니다. 명시적으로 제한자를 지정하지 않으면 기본적으로 public 가시성을 갖습니다.

각 스코프의 선언별로 제한자에 대한 설명은 아래를 참고합니다.

### packages

함수, 프로퍼티와 클래스, 오브젝트와 인터페이스는 “최상위 레벨”로 선언할 수 있습니다. 예를 들어 패키지 안에 바로 선언할 수 있습니다.

~~~kotlin
// 파일 이름 : example.kt
package foo

fun baz() {}

class Bar {}
~~~

- 만약 가시성 제한자를 지정하지 않으면 기본으로 public 을 사용합니다. 모든 곳에서 해당 선언에 접근할 수 있습니다.
- private 으로 지정하면 해당 선언을 포함한 파일에서만 접근할 수 있습니다.
- internal 로 지정하면 같은 모듈 안에서 접근할 수 있습니다.
- 최상위 레벨 선언은 protected 로 지정할 수 없습니다.

예제 :

~~~kotlin
// 파일 이름 : example.kt
package foo

private fun foo() {} // example.kt 에서만 접근 가능.

public var bar: Int = 5 // 프로퍼티는 모든 곳에서 접근 가능.
	private set         // setter 는 example.kt 에서만 접근 가능.
	
internal val baz = 6 // 같은 모듈 안에서 접근 가능.
~~~

### classes and interfaces

클래스 안에서 선언할 때 :

- Private 은 클래스의 모든 멤버를 포함한 클래스 안에서만 접근이 가능합니다.
- Protected 는 private 와 동일하고 하위 클래스에서 접근이 가능합니다.
- internal 은 선언한 클래스에 접근할 수 있는 모듈에 속한 모든 클라이언트가 internal 멤버에 접근이 가능합니다.
- public 은 선언한 클래스에 접근할 수 있는 모든 클라이언트가 public 멤버에 접근이 가능합니다.

Java 개발자 대상 _주의_ : Kotlin 에서 외부 클래스는 자신 내부 클래스에 있는 private 멤버에 접근할 수 없습니다.

예제 : 

~~~kotlin
open class Outer{
    private val a = 1
    protected val b = 2
    internal val c = 3
    val d = 4 // 기본적으로 public 입니다.
    
    protected class Nested{
        public val e: Int = 5
    }
}

class SubClass : Outer(){
    // a 에 접근 불가.
    // b, c 그리고 d 에 접근 가능.
    // Nested 와 e 에 접근 가능.
}

class Urelated(o: Outer){
    // o.a, o.b 에 적근 불가.
    // o.c(같은 모듈)와 o.d 에 접근 가능.
    // Outer.Nested 에 접근 불가, 그리고 Nested::e 에도 접근 불가.
}
~~~

### constructors

클래스의 주요 생성자에 가시성을 지정하려면 다음 구문을 사용합니다.(constructor 키워드를 사용해야 합니다.)

~~~kotlin
class C private constructor(a: Int) { ... }
~~~

여기서 생성자는 private 입니다. 모든 생성자의 기본 가시성은 public 이며, 클랠스에 접근 가능한 모든 곳에서 접근이 가능합니다.(예를 들어 internal 클래스의 생성자는 오직 동일 모듈에서만 접근이 가능합니다.)

### local declarations

로컬에 선언한 변수, 함수, 클래스는 가시성 제한자를 가질 수 없습니다.

### modules

internal 가시성 제한자는 같은 모듈에서 멤버에 접근할 수 있습니다. 더 구체적으로 말하면 모듈은 함계 컴파일되는 Kotlin 파일의 집합입니다.

- IntelliJ IDEA 모듈.
- Maven 이나 Gradle 프로젝트.
- 한 Ant 태스크 실행 시 컴파일되는 파일 집합.