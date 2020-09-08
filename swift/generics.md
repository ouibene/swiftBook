# Generics
제네릭을 사용하면 형식에 의존하지 않는 범용 코드를 작성할 수 있고, 코드의 재사용성과 유지보수의 편의성이 높아진다는 장점이 있다. 

## Generic Function
아래의 코드는 두 값을 교체하는 코드이다. swapInteger는 정수를 교체할 때 문제 없이 동작한다.
그러나 Int 외 다른 형식의 값은 받을 수 없다. (컴파일 에러가 발생)
다른 형식의 값을 교체하는 함수가 필요하다면, 필요한 형식의 수만큼 개별적으로 구현해야 한다.
실행에는 문제가 없는 코드지만, 함수 구현이 중복된다는 문제가 있다. 
<pre><code>func swapInteger(lhs: inout Int, rhs: inout Int) {
  let tmp = lhs
  lhs = rhs
  rhs = tmp
}

var a = 10
var b = 20

swapInteger(lhs: &a, rhs: &b)
a
b
</pre></code>

Generic function을 이용하여 문제를 해결해보자.
angle brackets 안 T는 타입 파라미터라고 한다. 타입 파라미터는 함수 내부에서 파라미터 형식이나 리턴형으로 사용된다.
함수 바디에서 사용하는 것도 가능하다. 보통은 T를 사용하지만, 다른 이름이라도 상관 없다. 
다만 형식 이름이기 때문에 Upper camel case를 사용한다.
그리고 두 개 이상을 선언하는 것도 가능하다. 
<pre><code>func name<T>(parameters) -> Type {
    code
}</pre></code>

앞의 함수를 제네릭 함수로 구현해보자.
<pre><code>func swapValue<T>(lhs: inout T, rhs: inout T) { //파라미터 형식도 Int 에서 T로 바꾸어 선언한다. 
    let tmp = lhs
    lhs = rhs
    rhs = tmp
} //이렇게 하면 형식에 관계 없이 모든 값을 파라미터로 전달 가능하다. 

a = 1
b = 2
swapValue(lhs: &a, rhs: &b)
a
b //값이 정상적으로 교체된다.

이번에는 double 형을 전달한다.
var c = 1.2
var d = 3.4
swapValue(lhs: &c, rhs: &d)
c
d //값이 정상적으로 교체된다.
</pre></code>

컴파일러는 전달된 형식에 적합한 코드를 자동으로 생성한다.
제네릭 함수는 형식에 관계 없이 하나의 구현으로 모든 자료형을 처리한다. 


### Type Parameter
타입 파라미터는 실제 자료형으로 대체되는 플레이스홀더이다. 타입파라미터를 T라고 선언했다고 해서 T라는 이름의 새로운 형식이 생성되는 것이 아니다.
코드가 실행될 때 문맥에 따라 실제 형식으로 대체되는 플레이스홀더일 뿐이다.



### Type Constraints
상기 코드를 조금 더 효율적으로 바꾸어본다.
<pre><code>func swapValue<T>(lhs: inout T, rhs: inout T) {
    if lhs == rhs { return } //ERROR! 
    /* 기본적으로 타입 파라미터가 대체하는 형식에는 제한이 없다. 바꾸어 말하면, 제네릭 함수로 전달하는 파라미터 형식에는 제한이 없다.
    Int에 저장된 값을 비교할 수 있는 이유는, 두 값을 비교할 수 있는 연산자가 구현되어있기 때문이다.
    직접 형식을 선언하면, 비교기능 역시 직접 구현해야 한다. 
    지금은 파라미터로 비교기능이 구현되지 않는 값도 전달될 수 있기 때문에 에러가 발생한다.
    타입 파라미터가 대체할 수 있는 형식을 equatable 프로토콜을 채용한 형식으로 제힌하면 문제가 해결된다.
    여기에 필요한 것이 type constraint 이다. 
    */
    
    let tmp = lhs
    lhs = rhs
    rhs = tmp
}</pre></code>


#### Type Constraints(형식 제약) 문법
아래와 같이 기술하면 타입파라미터가 대체할 수 있는 형식이 이 클래스와, 이 클래스를 상속한 형식으로 제한된다.
```
<TypeParameter: ClassName> 
```

프로토콜을 채용한 형식으로 제한된다.
``` 
<TypeParameter: ProtocolName>
```

<pre><code>func swapValue<T: Equatable>(lhs: inout T, rhs: inout T) {
    /* 위와 같이 Equatable 프로토콜을 채용하면 equal to 연산자를 통해 비교할 수 있는 값만 전달할 수 있게 된다. */
    if lhs == rhs { return } //(O)
    
    let tmp = lhs
    lhs = rhs
    rhs = tmp
}</pre></code>


### Specialization
제네릭 함수는 기본적으로 형식에 관계 없이 동일한 코드를 실행한다. 
상기 Type Constraint 문법에서 설명한 코드에서, 문자열을 비교한 경우에도 equal to 연산자로 비교한다.
대소문자를 무시하고 비교하고 싶다면 equal to 연산자 대신 caseInsensitiveCompare 메서드를 사용해야 한다.
그러나 이 메서드는 스트링 형식만 제공하기 때문에, if 문에서 직접 바꾸는 것은 불가능하다. 
이럴 때에는 특수화를 통하여 특정 형식을 위한 함수를 구현한다. 
<pre><code>func swapValue<T: Equatable>(lhs: inout T, rhs: inout T) {
    print("generic version")
    if lhs == rhs { return } 
    
    let tmp = lhs
    lhs = rhs
    rhs = tmp
}

func swapValue(lhs: inout String, rhs: inout String) {
    print("specialized version")
    
    if lhs.caseInsensitiveCompare(rhs) == .orderedSame {
        return
    }
    
    let tmp = lhs
    lhs = rhs
    rhs = tmp
}

var a = 1
var b = 2
swapValue(lhs: &a, rhs: &b) //generic version이 호출된다. 실제로 호출하는 함수는 전달하는 값을 통하여 결정된다.

var c = "Swift"
var d = "Programming"
swapValue(lhs: &c, rhs: &d) //specialized version이 호출된다. 

/* 특수화로 구현된 두번째 함수는 제네릭 함수를 오버라이딩 한 함수로 인식된다. 
그리고 제네릭 함수보다 우선순위가 높다. 그래서 모든 함수가 문자열을 받을 수 있지만, 우선순위가 높은 두번째 함수가 호출된다. */
</pre></code>













