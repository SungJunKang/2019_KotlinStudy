# Idioms

Kotlin에서 자주 사용하는 코딩 방식의 모음입니다.

### creating DTOs(POJOs/POCOs)

~~~kotlin
data class Customer(val name: String, val email: String)
~~~

이 코드는 다음 기능을 가진 Customer 클래스를 제공합니다.

- 모든 프로퍼티에 대한 getters(var 프로퍼티는 setters 포함)

- equals()
- hashCode()
- toString()
- copy()
- 모든 프로퍼티에 대해 Component1(), component2(), ...(Data 클래스 참조)

### Default values for function parameters

함수 파라미터의 기본 값.

~~~kotlin
fun foo(a: Int = 0, b: String = "") { ... }
~~~

### filtering a list

list 필터링.

~~~kotlin
val positives = list.filter{ x -> x > 0}
~~~

or 더 짧게

~~~kotlin
val positives = list.filter{ it > 0 }
~~~

### string Interpolation

문자열 삽입.

~~~kotlin
println("Name $name")
~~~

### instance chekcs

인스턴스 검사.

~~~kotlin
when(x){
    is Foo -> ...
    is Bar -> ...
    else -> ...
}
~~~

### traversing a map/list of pairs

map/list pair 탐색.

~~~kotlin
for((k, v) in map){
    println("$k -> $v")
}
~~~

k, v에 아무 이름이나 붙여도 됩니다.

### using ranges

범위 사용.

~~~kotlin
for(i in 1..100) { ... } // 폐쇄 범위: 100 포함.
for(i in 1 until 100) { ... } // 절반 열림 범위: 100 포함 안 함.
for(x in 2..10 step 2) {... }
for(x in 10 downTo 1) { ... }
if(x in 1..10 { ... }
~~~

### read-only list

읽기 전용 list.

~~~kotlin
val list = listOf("a", "b", "c")
~~~

### read-only mp

읽기 전용 map.

~~~kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
~~~

### accessing a map

map 접근.

~~~kotlin
print(map["key"])
map["key"] = value
~~~

### lazy property

지연 프로퍼티.

~~~kotlin
val p: String by lazy{
    // string을 계산합니다.
}
~~~

### extension functions

확장 함수.

~~~kotlin
fun String.spaceToCamelCase() { ... }

"Convert this to camelcase".spaceToCamelCase()
~~~

### creating a singleton

singleton 생성.

~~~kotlin
object Resource{
    val name = "Name"
}
~~~

### if not null shorthand

if not null 단축 표현.

~~~kotlin
val files = File("Test").listFiles()

println(files?.size)
~~~

### if not null and else shorthand

if not null과 else 단축 표현.

~~~kotlin
val files = File("Test").listFiles()

println(files?.size ?: "empty")
~~~

### executing a statement if null

Null 이면 문장 실행.

~~~kotlin
val values = ...
val email = values["email"] ?: throw IllegalStateException("Email is missing!")
~~~

### get first item of a possibly empty collection

빈 컬렉션의 첫 번째 항목 가져오기.

~~~kotlin
val emails = ... // 비어 있을 수 있음.
val mainEmail = emails.firstOrNull() ?: ""
~~~

### execute if not null

Null 이 아닌 경우 실행.

~~~kotlin
val value = ...

value?.let{
    ... // Null 이 아닌 경우 이 블록 실행
}
~~~

### map nullable value if not null

null 이 아닌 경우 null 가능 값 매핑.

~~~kotlin
val value = ...
val mapped = value?.let { transfromValue(it) } ?: defaultValueIfValueIsNull
~~~

### return on when statement

when statement의 반환.

~~~kotlin
fun transform(color: String): Int{
    return when(color){
    	"Red" -> 0
    	"Green" -> 1
    	"Blue" -> 2
    	else -> throw IllegalArgumentException("Invalid color param value")
	}
}
~~~

### 'try/catch' expression

try/catch 표현.

~~~kotlin
fun test(){
    val result = try{
        count()
    } catch)(e: ArithmenticException){
        throw IllegalStateException(e)
    }
    
    // 결과 작업
}
~~~

### 'if' expression

if 식.

~~~kotlin
fun foo(param: Int){
    val result = if(param == 1){
        "one"
    } else if(param == 2){
        "two"
    } else{
        "three"
    }
}
~~~

### builder-style usage of methods that return unit

Unit을 리턴하는 메서드를 빌더(Builder) 스타일로 사용

~~~kotlin
fun arrayOfMinusOnes(size: Int): IntArray{
    return IntArray(size).apply { fill(-1) }
}
~~~

### single-expression functions

한 개의 식을 갖는 함수.

~~~kotlin
fun theAnswewre() = 42
~~~

이 코드는 다음 코드와 동일합니다.

~~~kotlin
fun theAnswer(): Int{
    return 42
}
~~~

코드를 더 짧게 하기 위해 이 코드를 다른 이디엄과 함계 사용할 수 있습니다. 다음은 when 식과 함께 사용한 예입니다.

~~~kotlin
fun transform(color: String): Int = when(color){
	"Red" -> 0
	"Green" -> 1
	"Blue" -> 2
	else -> throw IllegalArgumentException("Invalid color param value")
}
~~~

### calling multiple methods on an object instance('with')

객체 인스턴스의 여러 번 메서드 호출하기.

~~~kotlin
class Turtle{
    fun penDown()
    fun penUp()
    fun turn(degress: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()

with(myTurtle){
    penDown()
    for(i in 1..4){ // 100pix 사각형 그리기
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
~~~

### Java 7's try with resources

Java 7의 try-with-resources.

~~~kotlin
val stream = Files.newInputStream(Path.get("/some/file.txt"))

stream.buffered().reader.use{ reader ->
	println(reader, readText())
}
~~~

### convenient form for a generic function that requires the generic type information

제네릭 타입 정보가 필요한 제네릭 함수를 위한 간편 형식.

~~~kotlin
public final class Gson{
    ...
    public <T> T fromJson(JsonElement json, Class<T> classOfT) throws JsonSyntaxException{
    ...
line fun <reified T: Any> Gson.fromJson(json: JsonElement): T = this.fromJson(json, T::class.java)
~~~

### consuming a nullable boolean

nullable boolean 사용.

~~~
val b: Boolean? = ...
if(b == true){
    ...
}
else{
    // b는 거짓이거나 null 입니다.
}
~~~

