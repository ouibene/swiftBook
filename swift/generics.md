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

## Generic Types

컬렉션에는 동일한 형식의 값만 저장할 수 있다. 제네릭 타입을 공부하고 나면 그 이유를 알 수 있다. 

### 선언
제네릭 타입은 기존 형식에 타입 파라미터를 추가하면 제네릭 타입으로 선언된다. 
타입 파라미터는 형식 이름 뒤에 선언한다.
문법은 제네릭 함수에서 공부했던 것과 동일하고, 형식 제약 문법도 동일하다.
속성의 자료형, 메서드의 리턴형, 파라미터 형식처럼 형식 내부에서 사용되는 형식들을 타입 파라미터로 대체할 수 있다.
```
class Name<T> {
    code
}

struct Name<T> {
    code
}

enum Name<T> {
    code
}
```

```
struct Color<T> {
    var red: T
    var green: T
    var blue: T
}

var c = Color(red: 128, green: 80, blue: 200)
/* 여기에서 c의 자료형은 Color<Int> 이다. 여기에서의 Int는 타입파라미터를 대체하는 형식이다.
생성자를 호출하면서 파라미터로 Int의 값을 전달해서, 타입파라미터가 Int로 추론되었기 때문이다. */

c = Color(red: 128.0, green: 80.0, blue: 200.0) //Error! Cannot assign value of type 'Color<Double>' to type 'Color<Int>'
/* 제네릭 타입의 인스턴스를 생성하면 컴파일러가 타입 파라미터에 적합한 형식을 자동으로 생성한다. 
   여기에서는 타입파라미터가 Int, Double 이렇게 하나씩 만들어진다. 그리고 두 형식은 별도의 형식으로 처리된다. */
```

에러인 코드를 지우고 새로운 상수에 인스턴스를 저장해보자.
그리고 이번에는 형식을 직접 선언한다. 
```
let d: Color = Color(red: 128.0, green: 80.0, blue: 200.0) //(O) 이 경우에는 이전 코드처럼 타입파라미터가 자동으로 추론된다. 
```

아래의 경우에는 더이상 추론할 수 없게 되고, 타입파라미터의 형식을 직접 선언해야 한다. 
```
let d: Color //Error!
```

아래와 같이 타입파라미터의 형식을 직접 선언해본다.
```
let d: Color<Int>
```

새로운 배열을 하나 선언해보자.
```
let arr: [Int]
```
위 코드는 단축 문법으로 선언한 상수이다.
단축 문법이 아닌 정식 문법으로 선언해보자.

아래와 같이 Array<Int>라고 선언한다.
Array 형식에는 배열을 처리하는 다양한 코드가 있다. 다양한 형식을 처리할 수 있도록 제네릭 타입으로 구현되어 있고,
<Int>라고 선언하면 컴파일러가 Int를 처리하는 코드를 자동으로 생성한다. 
```
let arr: Array<Int>
```

딕셔너리도 마찬가지이다.
```
let dict: Dictionary<String, Double>
```


extension을 이용하여 제네릭 타입을 확장해보자.
제네릭 타입을 확장할 때는 타입 파라미터를 형식 이름 뒤에 추가하지 않는다. 
extension Color<T> 라고 사용하면 컴파일 에러가 발생한다. 형식에서 선언한 타입 파라미터를 변경하는 것이 불가능하다. 
```
struct Color<T> {
    var red: T
    var green: T
    var blue: T
}
  
extension Color {
    func getComponents() -> [T] { //익스텐션에서도 동일한 파라미터를 사용해야 한다. 메서드의 리턴형을 타입 파라미터로 선언할 때에도 T를 사용한다. 
      return [red, green, blue]
    }
}

익스텐션을 추가한 멤버는 기본적으로 타입 파라미터에 관계 없이 모든 컬러 형식에 추가된다. 

let intColor = Color(red: 1, green: 2, blue: 3) //타입 파라미터가 Int로 추론된다. 
intColor.getComponents() // intColor 메서드의 리턴형은 보면 Int 이다.

let doubleColor = Color(red: 1.0, green: 2.0, blue: 3.0) //타입 파라미터가 Double로 추론된다.
doubleColor.getComponents() //메서드의 리턴형은 더블이다.
```


타입 파라미터에 형식 제약을 추가할 수 있는 것 처럼, 익스텐션에서 확장 대상을 제한하는 것도 가능하다.
형식 이름에 where 를 추가해보자. 
```
struct Color where T: FixedWidthInteger { 
    var red: T
    var green: T
    var blue: T
}
  
extension Color {
    func getComponents() -> [T] {
      return [red, green, blue]
    }
}

익스텐션을 추가한 멤버는 기본적으로 타입 파라미터에 관계 없이 모든 컬러 형식에 추가된다. 

let intColor = Color(red: 1, green: 2, blue: 3) 
intColor.getComponents()

let doubleColor = Color(red: 1.0, green: 2.0, blue: 3.0) 
doubleColor.getComponents() //Error! 
/* FixedWidthInteger 프로토콜은 Int형식이 채용하고 있지만 Double형식은 채용하고 있지 않다.
   타입 파라미터가 Int로 추론되는 형식에는 메서드가 추가되지만, Double인 경우 추가되지 않는다. 
   추가되지 않은 메서드를 호출하는 마지막 코드에서 에러가 발생하는 것이다. */
```

이 프로토콜을 채용한 모든 형식에 메서드가 추가된다. 만약 Int 형식에만 추가하고 싶다면 
```
struct Color where T == Int { 
```
이렇게 대상 형식을 직접 지정하도록 한다.


## Associated Types
제네릭 프로토콜을 선언하는 방법

프로토콜이 하나 선언되어 있고 메서드 두개가 선언되어 있다. 
이 프로토콜을 채용한 형식은 메서드를 구현할 때 파라미터의 자료형과 리턴형을 반드시 일치시켜야 한다.
```
protocol QueueCompatible{
  func enqueue(value: Int) //value parameter의 자료형을 Int로 선언하여야만 한다.
  func dequeue() -> Int?
}
```

위 프로토콜을 제네릭 프로토콜로 바꾸어 보자.
```
protocol QueueCompatible<T>{//Error!
  func enqueue(value: T)    //Error!
  func dequeue() -> T?      //Error!
}
```
  
제네릭 프로토콜을 선언할 때는 Associated Types(연관 형식)을 사용해야 한다.
associated type은 다음과 같이 선언한다. 
```
associatedtype Name //이름은 Upper camel case로 짓는 것이 컨벤션이다.
```
연관 형식은 타입 파라미터와 마찬가지로 프로토콜 내에서 실제 형식으로 대체되는 placeholder이며 새로운 형식은 아니다.

```
protocol QueueCompatible{
  associatedtype Element
  func enqueue(value: Element) 
  func dequeue() -> Element?
}
```
연관형식은 프로토콜에서 사용하는 플레이스 홀더 형식이다. 요구사항을 선언하는 것은 아니다.
따라서 프로토콜을 채용한 형식에서 다시 연관 형식을 선언 할 필요는 없다. 

연관 형식을 대체하는 실제 형식은 프로토콜을 채용한 형식에 따라서 결정된다.
연관 형식이 선언된 프로토콜을 채용한 형식은 typealias 키워드로 연관 형식의 실제 형식을 선언해야 한다.
```
typealias AssociatedTypeName = Type
```

```
protocol QueueCompatible{
  associatedtype Element
  func enqueue(value: Element) 
  func dequeue() -> Element?
}

class IntegerQueue: QueueCompatible {
  typealias Element = Int //연관 형식을 Int로 선언
  
  func enqueue(value: Int) { //앞에서 연관 형식을 Int로 선언했기 때문에 파라미터 형식도 Int가 되어야 한다. 
                             //만약 다른 형식으로 선언하면 프로토콜의 요구사항을 충족시키지 못한다. 
    
  }
  
  func dequeue() -> Int? {   //dequeue method 또한 리턴 형식을 맞춰 주어야 한다. 
    return 0
  }
}

class DoubleQueue: QueueCompatible {
  //이번에는 연관 형식을 생략하고 바로 메서드를 구현해보자.
  func enqueue(value: Double) {
  
  }
  
  func dequeue() -> Double? {
    return 0
  }
} //이런 식으로 선언하면 연관 형식이 Double로 추론된다.
  //사용된 실제 형식을 통해서 연관 형식을 추론할 수 있기 때문에 연관 형식을 생략해도 문제가 없다. 
  //그래서 typealias를 많이 생략한다. 
```

연관 형식에 제약을 추가하는 방법은 타입파라미터와 동일하다.
연관 형식을 equatable 프로토콜을 채용한 형식으로 제한하고 싶다면
연관 형식 이름 뒤에 콜론을 쓰고 프로토콜을 추가하면 된다. 
```
protocol QueueCompatible{
  associatedtype Element: Equatable //이렇게 연관 형식의 이름 뒤에 콜론을 쓰고 프로토콜을 추가한다. 
  func enqueue(value: Element) 
  func dequeue() -> Element?
}
```
