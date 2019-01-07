## 기본문법 

### defining packages

패키지명은 항상 파일의 최상단에 위치합니다. 

```kotlin
package my.demo

import java.util.*
```

디렉터리와 패키지를 일치시킬 필요는 없습니다. 원본 파일은 파일 시스템에 임의로 배치될 수 있습니다.

### defining functuons

Int return type을 가진 두 개의 Int 매개 변수가 있는 함수.

~~~kotlin
fun sum(a: Int, b: Int): Int{
    return a + b
}
~~~

표현식 본문 및 Type 추론이 있는 함수.

~~~kotlin
fun sum(a: Int, b: Int) = a + b
~~~

의미 없는 값을 반환하는 함수.

~~~kotlin
fun printSum(a: Int, b: Int): Unit{
    println("Sum of $a and $b is ${a + b}")
}
~~~

단위 반환 유형은 생략할 수 있습니다.

~~~kotlin
fun printSum(a: Int, b: Int){
    println("Sum of $a and $b is ${a + b}")
}
~~~

### defining variables

val를 사용하여 읽기 전용 로컬 변수를 정의합니다. val는 값을 한 번만 할당할 수 있습니다.

~~~kotlin
val a: Int = 1 // 즉각적인 선언
val b = 2 // Int Type 추론
val c: Int // Initializer를 제공하지 않을 때 필요한 유형
c = 3 // 연기된 선언
~~~

재할당할 수 있는 변수는 var를 사용합니다.

~~~kotlin
var x = 5 // Int Type 추론
x += 1
~~~

최상위 변수.

~~~kotlin
val PI = 3.14
var x = 0

fun incrementX(){
    x += 1
}
~~~

### comments

Java와 Javascript처럼 Kotlin은 end-of-line과 block comments를 지원합니다.

~~~kotlin
// 이것은 end-of-line comment입니다.

/* 이것은 여러 줄이 가능한
   block comment입니다. */
~~~

Java와 달리 Kotlin의 block comment는 중첩될 수 있습니다.

### using string templates

~~~kotlin
var a = 1
// 템플릿의 쉬운 이름
val s1 = "a is $a"

a = 2
// 템플릿의 임의 표현식
val s2 = "{$s1.replace("is", "was")}, but now is $a"
~~~

### using conditional expressions

~~~kotlin
fun maxOf(a: Int, b: Int): Int{
    if(a > b){
        return a
    }
    else{
        return b
    }
}
~~~

표현식으로 사용.

~~~kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b
~~~

### using nullable values and checking for nulll

Null 값이 가능할 경우 값을 Nullable로 명시적으로 표시해야 합니다.

str에 정수가 없으면 null을 반환.

~~~kotlin
fun parseInt(str: String): Int?{
    // ...
}
~~~

null 가능 값을 반환하는 함수를 사용.

~~~kotlin
fun printProduct(arg1: String, arg2: String){
    val x = parseInt(arg1)
    val y = parseInt(arg2)
    
    // x * y를 사용하면 null이 있을 수 있으므로 오류가 발생한다.
    if(x != null && y != null){
        // null 검사 후 x와 y가 자동으로 null 불가 상태로 주조됨.
        println(x * y)
    }
    else{
        println("either '$arg1' or '$arg2' is not a number")
    }
}
~~~

or

~~~kotlin
// ...
if(x == null){
    println("Wrong number format in arg1: '$arg1'")
    return
}
if(y == null){
    println("Wrong number format in arg2: '$arg2'")
    return 
}

// null 검사 후 x와 y가 자동으로 null 불가 상태로 주조됨.
print(x * y)
~~~

### using type checks and automatic casts

연산자는 표현식이 type의 인스턴스(instance)인지 확인합니다. 변경할 수 없는 로컬 변수 또는 속성이 특정 유형에 대해 선택된 경우 명시적으로 지정할 필요가 없습니다.

~~~kotlin
fun getStringLength(obj: Any): Int?{
    if(obj is String){
        // obj는 이 지점에서 자동으로 String에 캐스팅됩니다.
        return obj.length
    }
    
    // obj는 여전히 type-checked 지점 외부에 있는 any형입니다.
    return null
}
~~~

or

~~~kotlin
fun getStringLength(obj: Any): Int?{
    if(obj !is String){
        return null
    }
    
    // obj는 이 지점에서 자동으로 String에 캐스팅됩니다.
    return obj.length
}
~~~

or even

~~~kotlin
fun getStringLength(obj: Any): Int?{
    // obj는 &&의 오른쪽에 있는 string에 자동으로 캐스팅됩니다.
    if(obj is String && obj.length > 0){
        return obj.length
    }
    
    return null
}
~~~

### using a for loop

~~~kotlin
var items = listOf("apple", "banana", "kiwifruit")

for(item in items){
    println(item)
}
~~~

or

~~~kotlin
var items = listOf("apple", "banana", "kiwifruit")

for(index in items.indices){
    println("item at $index is ${items[index]}")
}
~~~

### using a while loop

~~~kotlin
var items = listOf("apple", "banana", "kiwifruit")
var index = 0

while(index < items.size){
    println("item at $index is ${items[index]}")
    index++
}
~~~

### using when expression

~~~kotlin
fun describe(obj: Any): String =
	when(obj){
        1 -> "One"
        "Hello" -> "Greeting"
        is Long -> "Long"
        !is String -> "Not a string"
        else -> "Unknown"
	}
~~~

### using ranges

연산자를 사용하여 숫자가 범위 내에 있는지 확인합니다.

~~~kotlin
val x = 10
val y = 9

if(x in 1..y+1){
    println("fits in range")
}
~~~

숫자가 범위를 벗어났는지 확인합니다.

~~~kotlin
val list = listOf("a", "b", "c")

if(-1 !in 0..list.lastIndex){
    println("-1 is out of range")
}
if(list.size !in list.indices){
    println("list size is out of valiud list indices range, too")
}
~~~

범위 사이를 반복합니다.

~~~kotlin
for(x in 1..5){
    print(x)
}
~~~

또는 다음 단계를 진행합니다.

~~~kotlin
for(x in 1..10 step 2){
    print(x)
}

println()

for(x in 9 downTo 0 step 3){
    print(x)
}
~~~

### using collections

컬렉션에 반복합니다.

~~~kotlin
for(item in items){
    print(item)
}
~~~

컬렉션에 연산자를 사용하여 개체가 포함되어 있는지 확인합니다.

~~~kotlin
when{
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
~~~

람다 식을 사용하여 컬렉션 필터링 및 매핑합니다.

~~~kotlin
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")

fruits
	.filter { it.startsWith("a") }
	.sortedBy { it }
	.map { it.toUpperCase() }
	.forEach { println(it) }
~~~

### creating basic classes and their instances

~~~kotlin
val rectangle = Reatangle(5.0, 2.0) // 'new' 키워드 필요 없음.
val triangle = Triangle(3.0, 4.0, 5.0)
~~~

