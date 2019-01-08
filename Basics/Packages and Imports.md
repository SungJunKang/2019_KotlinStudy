## 패키지와 임포트

### packages

소스 파일은 패키지 선언으로 시작합니다.

~~~kotlin
package foo.bar

fun baz(){}

class Goo {}

// ...
~~~

클래스나 함수와 같은 소스 파일의 모든 내용은 선언한 패키지에 속합닏자. 위 예의 경우 baz()의 전체 이름은 foo.bar.baz 이고 Goo 의 전체 이름은 foo.bar.Goo 입니다.

패키지를 지정하지 않으면 파일의 모든 내용은 이름이 없는 "기본(default)" 패키지에 속합니다.

### import

기본 import 외에 각 파일은 자신의 import 디렉티브를 포함할 수 있습니다.

다음은 한 개 이름을 import 하는 예입니다.

~~~kotlin
import foo.Bar // Bar에 전체 이름을 사용하지 않고 접근할 수 있음.
~~~

해당 범위의 모든 요소(패키지, 클래스, 오브젝트 등)에 접근할 수도 있습니다.

~~~kotlin
import foo.* // 'foo'의 모든 요소에 접근 가능.
~~~

이름이 충돌하면 as 키워드로 로컬에서 사용할 이름을 변경해서 충돌을 피할 수 있습니다.

~~~kotlin
import foo.Bar // Bar로 접근.
import bar.Bar as bBar // bBar는 'bar.Bar'를 의미.
~~~

Import 키워드는 클래스뿐만 아니라 다른 것도 import 할 수 있습니다.

- 최상위 레벨 함수와 프로퍼티.
- 오브젝트 선언의 함수와 프로퍼티.
- 열거형 상수.

Java와 달리 Kotlin은 별도의 "import static" 구문을 지원하지 않습니다. import 키워드를 사용해서 모든 선언을 import 할 수 있습니다.

### visibility of top-level declarations

최상위 선언이 private이면 그것이 선언된 파일에 대해 private 입니다.