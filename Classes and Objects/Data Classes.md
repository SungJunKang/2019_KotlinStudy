## 데이터 클래스

종종 데이터만 갖고 다른 건 하지 않는 클래스를 만듭니다. 보통 그런 클래스의 표준 기능은 데이터로부터 기계적으로 만듭니다. Kotlin 에서는 이를 _ 데이터 클래스 _ 라 부르며 data 로 데이터 클래스를 지정합니다.

~~~kotlin
data class User(val name: String, val age: Int)
~~~

컴파일러는 주요 생성자에 정의한 모든 프로퍼티로부터 다음의 멤버를 자동으로 생성합니다.

- equals() / hashCode() 쌍
- "User(name=John, age=42)" 혁식의 toString()
- 프로퍼티 선언 순서에 따라 프로퍼티별로 대응하는 componetN() 함수
- copy() 함수

이 함수를 클래스 몸체에 정의하거나 베이스 타입에서 상속받을 경우 생성하지 않습니다.

생성한 코드가 일관되고 의미있는 기능을 갖도록 하기 위해 데이터 클래스는 다음을 충족해야합니다.

- 주요 생성자는 최소 한 개 파라미터가 필요합니다.
- 모든 주요 생성자 파라미터는 val 이나 var 로 지정해야 합니다.
- 데이터 클래스는 추상, open, 실드, 내부(inner) 일 수 없습니다.
- 데이터 클래스는 다른 클래스를 확장할 수 없습니다(인터페이스 구현은 됩니다.)

~~~kotlin
// JVM 의 경우, 생성한 클래스가 파라미터 없는 생성자를 필요로 하면 모든 프로퍼티에 대해 기본 값을 지정해야 합니다.

data class User(val name: String = "", val age: Int = 0)
~~~

### copying

객체를 복사할 때 종종 일부 프로퍼티만 변경하고 나머지는 그대로 유지하고 싶을 때가 있습니다. 이를 위해 copy() 함수를 생성합니다. 앞서 User 클래스의 복사 구현은 다음과 같습니다.

~~~kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
~~~

다음과 같이 코드를 작성할 수 있습니다.

~~~kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
~~~

### data classes and destructuring declarations

데이터 클래스를 위해 생성한 _ 컴포넌트 함수 _ 는 분해 선언에 데이터를 사용할 수 있도록 합니다.

~~~kotlin
val jane = User("Jane", 35)
val (name, age) = jane

println("$name, $age years of age") // "Jane, 35 years of age" 출력.
~~~

### standard data classes

표준 라이브러리는 Pair 와 Triple 을 제공합니다. 많은 경우 이름을 갖는 데이터 클래스를 사용하는 것이 더 좋은 설계가 됩니다. 왜냐하면 프로퍼티를 위한 의미 있는 이름을 제공함으로써 코드 가독성이 높아지기 때문입니다.