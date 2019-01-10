## 실드 클래스

가질 수 있는 타입을 특정 타입 집학으로 제한하고 다른 타입은 가질 수 없도록 하고 싶을 때, 클래스 상속을 제한할 목적으로 실드 클래스를 사용합니다. 실드 캘래스는 어떤 의미에서 열거형 클래스의 확장입니다. 열거 타입은 제한된 값 집합을 갖지만, 각 열거형 상수의 인스턴스는 오직 한 개만 존재합니다. 반면에 실드 클래스의 하위 클래스는 상태를 포함할 수 있는 여러 인스턴스를 가질 수 있습니다.

실드 클래스를 선언하려면 클래스 이름 앞에 sealed 제한자를 쓰면 됩니다. 실드 클래스는 하위 클래스를 가질 수 있지만 모든 하위 클래스는 실드 클래스 선언 자체에 중첩해야 합니다.

~~~kotlin
sealed class Expr{
    class Const(val number: Double) : Expr()
    class sum(val e1: Expr, val e2: Expr) : Expr()
    object NotANumber : Expr()
}
~~~

실드 클래스의 하위 클래스를 확장하는 클래스(indirect inheritors) 는 어디든 위치할 수 있습니다. 실드 클래스 선언 내부에 위치할 필요는 없습니다.

실드 클래스의 최대 장접은 when 식과 함께 사용할 수 있다는 점입니다. 모든 경우를 확실하게 다루고 있다면 else 절을 추가할 필요가 없습니다.

~~~ kotlin
fun eval(expr: Expr): Double = when(expr){
    is Expr.Const -> expr.number
    is Expr.Sum -> eval(expr.e1) + eval(expr.e2)
    Expr.NotANumber -> Double.NaN
    // 모든 경우를 다루기 때문에 'else' 절이 필요 없습니다.
}
~~~

