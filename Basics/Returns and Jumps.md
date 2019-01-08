## 리턴과 점프

### returns and jumps

Kotlin은 세 가지 구조적인(structural) 점프 연산자를 제공합니다.

- return. 기본적으로 가장 가깝게 둘러 싼 함수나 임의 함수에서 리턴합니다.
- break. 가장 가깝게 둘러 싼 루프를 끝냅니다.
- continue. 가장 가깝게 둘러 싼 루프의 다음으로 넘어갑니다.

### break and continue labels

Kotlin 의 모든 식은 라벨로 표식을 가질 수 있습니다. 라벨은 @ 기호 뒤에 구분자를 갖습니다. 예를 들어 abc@, fooBar@ 는 유효한 라벨입니다. 식에 라벨을 붙이려면 식 앞에 라벨을 위치시키면 됩니다.

~~~kotlin
loop@ for(i int 1..100){
    // ...
}
~~~

이제 break 나  continue 를 라벨로 한정할 수 있습니다.

~~~kotlin
loop@ for(i in 1..100){
    for(j in 1..100){
        if(...){
            break@loop
        }
    }
}
~~~

라벨로 한정한 break 는 그 라벨이 붙은 루프 이후로 실행 지점을 이동합니다. continue 는 루프의 다음 반복을 처리합니다.

### Return at labels

Kotlin 은 함수 리터럴, 로컬 함수와 객체 식에서 함수를 중첩할 수 있습니다. 한정한 return 은 바깥(outter) 함수에서 리턴할 수 있도록 합니다. 가장 중요한 쓰임새는 람다 식에서 리턴하는 것입니다.

~~~kotlin
fun foo(){
    ints.forEach{
        if(it == 0){
            return
        }
        print(it)
    }
}
~~~

return 식은 가장 가깝게 둘러싼 함수인 foo 에서 리턴합니다. (비 로컬(non-local) 리턴은 인라인 함수로 전달한 람다 식만 지원합니다.) 만약 람다 식에서 리턴하고 싶다면 라벨을 붙여서 return 을 한정해야 합니다.

~~~kotlin
fun foo(){
    ints.forEach lit@{
        if(it == 0) {
        	return@lit
    }
    print(it)
}
~~~

이제 이 return 은 람다 식에서 리턴합니다. 종종 임의 라벨을 사용하는 게 더 편리합니다. 임의 라벨은 람다를 전달 받은 함수와 같은 이름을 갖습니다.

~~~kotlin
fun foo(){
    ints.forEach{
        if(it == 0){
            return@forEach
        }
        print(it)
    }
}
~~~

다른 방법으로 람다 식 대신 임의 함수를 사용할 수 있습니다. 임의 함수에서 return 문은 임의 함수 자체에서 리턴합니다.

~~~kotlin
fun foo(){
    ints.forEach(fun(value: Int){
        if(value == 0){
        return
        }
        print(value)
    })
}
~~~

값을 리턴할 때 파서는 한정 리턴에 우선순위를 줍니다.

~~~kotlin
return@a 1
~~~

​	이는 "라벨 식(@a 1)을 리턴한다"가 아닌 "@a에 1을 리턴한다"를 의미합니다.