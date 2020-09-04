# Protocol
형식에서 공통적으로 제공하는 멤버 목록을 의미한다.
형식에서 구현해야 하는 멤버가 선언되어 있고, 실제 구현은 프로토콜에 포함되어 있지 않다. 대신 클래스와 구조체같은 형식들이 프로토콜에 선언되어 있는 멤버들을 실제로 구현한다. 
이를 프로토콜을 따른다 또는 프로토콜을 채용한다고 말한다 (Adopting Protocols). 프로토콜을 채용한 형식은 반드시 프로토콜에 선언되어 있는 필수 멤버(요구사항이라고 하기도 한다)를 모두 구현해야 한다.

## Protocol 사용법
<pre><code>protocol ProtocolName {
  ...
}</pre></code>
Upper camel case로, Protocol 키워드를 이용하여 선언한다.
멤버는 선언만 하고, 구현은 포함하지 않는다.

<pre><code>protocol ProtocolName: Protocol, ... {
  ...
}</pre></code>
프로토콜은 상속을 지원한다.
클래스 상속과 동일하다. 그러나 클래스와는 다르게 다중상속이 가능하다.

## 예제
<pre><code>protocol Something {
  func doSomething()
}
// 이제 Something 프로토콜을 채용한 형식은 doSomething 메서드를 구현해야만 한다. 
그러나 이것이 끝이 아니라, 반드시 프로토콜을 채용하겠다고 명시적으로 선언해야만 한다.</pre></code>

## Adopting Protocols
<pre><code>enum TypeName: ProtocolName, ... {

}

struct TypeName: ProtocolName, ... {

}

class TypeName: SuperClass, ProtocolName, ... {

}</pre></code>


<pre><code>struct Size: Something {
  // 아무것도 작성하지 않으면 에러가 발생한다.
  // Type 'Size' does not conform to protocol 'Something'
  
  func doSomething() { //프로토콜 채용시 헤더만 일치시키고 바디는 자유롭게 구현한다.
    
  }
}</pre></code>

## Class-Only Protocols
프로토콜을 채용할 수 있는 형식에는 제한이 없으나, AnyObject 프로토콜을 상속하면 클래스 전용 프로토콜로 선언된다. 
구조체나 열거형에서는 이 프로토콜을 채용할 수 없게 된다. 

<pre><code>protocol ProtocolName: AnyObject {

}</pre></code>

<pre><code>protocol SomethingObject: AnyObject, Something {

}

struct Value: SomethingObject {

} // (X) Error! / Non-class type 'Value' cannot conform to class protocol 'SomethingObject'

class Object: SomethingObject {
  func doSomething() {
  
  } //Something protocol 에 있는 메서드 선언을 상속받으므로, 멤버인 doSomething 메서드를 구현해야 한다. 
}</pre></code>

## Property Requirements
프로토콜에서 속성을 선언하고 형식에서 속성 요구사항을 구현하는 방법에 대해 후술한다.

<pre><code>protocol ProtocolName {
  var name: Type { get set }
  static var name: Type { get set }
}</pre></code>

형식에서 속성을 선언할 때에는 가변성에 따라 let 이나 var 를 선언하지만, 프로토콜에서는 var 키워드로만 선언한다.
프로토콜에서의 var 키워드는 가변성과는 관계가 없으며 선언하는 멤버가 속성임을 나타낼 뿐이다.

brace 사이에 get, set 이 위치하는데, 이 두 키워드가 속성의 가변성을 결정한다.
get, set이 모두 선언되어 있다면 형식에서 읽기와 쓰기가 모두 가능한 속성을 선언해야 한다.
get 만 포함된 경우에는 형식에서 읽기전용 속성으로 선언해도 되고, 읽기 쓰기가 모두 가능한 속성을 선언해도 문제 없다. 

형식 속성으로 선언할 때에는 var 키워드 앞에 static 키워드를 추가한다.

<pre><code>protocol Figure {
  var name: String { get }
}

struct Rectangle: Figure {
  // 프로토콜에 선언되어있는 속성을 형식에 추가해야 한다. 이름과 형식을 모두 맞추어 주도록 한다.
  let name: "Rectangle" //상수 저장속성으로 선언하면 요구사항을 충족한다.
}

struct Triangle: Figure {
  var name: "Triangle" //get만 포함되었다고 해서 읽기 전용 속성으로 선언해야 하는 것은 아니다.
}

struct Circle: Figure {
  var name: String {
    return "Circle"
  } //이렇게 선언하더라도 요구사항을 충족한다.
}</pre></code>


<pre><code>protocol Figure {
  var name: String { get set }
}

struct Rectangle: Figure {
  let name: "Rectangle" // ERROR! 읽기와 쓰기가 가능한 속성으로 선언하여야 한다. 값을 쓰는 것이 불가능하기 때문이다. let 키워드를 var 로 바꾸도록 한다.
}

struct Triangle: Figure {
  var name: "Triangle" // 요구사항을 충족한다.
}

struct Circle: Figure {
  var name: String {
    return "Circle"
  } // ERROR! 읽기 전용 계산속성이다. set 블록을 추가한다.
}</pre></code>

<pre><code>struct Circle: Figure {
  var name: String {
    get {
      return "Circle"
    } 
    set {
    
    }
  }
}</pre></code>


이번에는 var 키워드 앞에 static 키워드를 추가하도록 한다.
이렇게 하면 모든 속성을 형식 속성으로 선언하여야 한다. 

<pre><code>protocol Figure {
  static var name: String { get set }
}

struct Rectangle: Figure {
  static var name: "Rectangle"

struct Triangle: Figure {
  static var name: "Triangle" 
}

struct Circle: Figure {
  static var name: String {
    get {
      return "Circle"
    } 
    set {
    
    }
  }
}</pre></code>

circle 구조체를 클래스로 바꾸어본다. - (1)
static 키워드로 선언된 속성은 서브클래스로 상속되지만 오버라이딩은 불가하다.
오버라이딩을 허용하려면 static 키워드 대신 class 키워드로 선언한다. - (2)

<pre><code>class Circle: Figure { - (1)
  class var name: String { - (2) name 속성은 여전히 형식 속성이고, 이름과 자료형, 가변성도 동일하다. 그렇기 때문에 프로토콜 요구사항을 충족한다. 서브클래스에서 오버라이딩 하는 것도 가능하다.
    get {
      return "Circle"
    } 
    set {
    
    }
  }
}</pre></code>

프로토콜에서 static으로 선언되어 있다고 하여 형식에서도 static으로 선언해야 하는 것은 아니다.
static과 class 으로 선언되어 있는 속성 모두 요구사항을 충족한다. 


## Method Requirements
Protocol 에서 method 를 작성할 때에는 아래와 같이 헤더만 작성한다. 
typemethod 를 선언할 때에는 func 앞에 static 을 추가한다. 
만약 프로토콜을 값 형식이 채용할 수 있고, 메서드 내부에서 속성 값을 변경해야 한다면 mutating 키워드를 사용한다. 
이 때의 mutating 은 값 형식 전용이라는 것을 나타내기 보다는 메서드가 속성 값을 변경할 수 있는 역할을 할 수 있게끔 하는 키워드라고 보아야 한다. 
따라서 참조 형식에서도 채용이 가능하다. 
<pre><code>protocol ProtocolName {
  func name(param) -> ReturnType
  static func name(param) -> ReturnType
  mutating func name(param) -> ReturnType
}</pre></code>

<pre><code> protocol Resettable {
  func reset()
}

class Size: Resettable {
  var width = 0.0
  var height = 0.0
  
  func reset() {
    width = 0.0
    height = 0.0
  }
}
</pre></code>
프로토콜은 메서드의 실제 구현은 상관하지 않는다. 
메서드 이름, 파라미터 형식, 리턴형만 일치시켜 주면 된다.

<pre><code>protocol Resettable { 
  func reset()
}

struct Size: Resettable { // struct 키워드를 사용하면...
  var width = 0.0
  var height = 0.0
  
  func reset() {
    width = 0.0   //Error! Cannot assign to property: 'self' is immutable
    height = 0.0  //Error! Cannot assign to property: 'self' is immutable
  }
}
</pre></code>
값 형식의 인스턴스 메서드에서 속성 값을 바꾸려면 func 키워드 앞에 mutating 키워드를 추가해야 한다. 

<pre><code>protocol Resettable { 
  func reset()
}

struct Size: Resettable { //Error! 
  var width = 0.0
  var height = 0.0
  
  mutating func reset() { //값 형식에서 속성을 바꾸는 메서드라면 프로토콜에서 선언할 때에도 mutating 키워드를 추가한다. 
    width = 0.0  
    height = 0.0 
  }
}
</pre></code>

<pre><code>protocol Resettable { 
  mutating func reset()
}

struct Size: Resettable { 
  var width = 0.0
  var height = 0.0
  
  mutating func reset() {
    width = 0.0  
    height = 0.0 
  }
}
</pre></code>

Size 구조체를 다시 class 로 바꾸어본다. mutating 는 값 형식에서만 사용하는 키워드이므로 삭제한다.
<pre><code>struct Resettable { 
  mutating func reset()
}

class Size: Resettable { 
  var width = 0.0
  var height = 0.0
  
  func reset() {
    width = 0.0  
    height = 0.0 
  }
}
</pre></code>
Class 에서는 mutating 키워드를 추가하지 않더라도 메서드에서 속성을 바꿀 수 있다. 

이번에는 reset method를 type method 로 선언한다. 
<pre><code>struct Resettable { 
  statuc func reset()
}

class Size: Resettable { 
  var width = 0.0
  var height = 0.0
  
  func reset() {
    width = 0.0  
    height = 0.0 
  }
  
  static func reset() {
  
  }
}
</pre></code>
오버로딩 규칙에 따라서 이름이 동일한 메서드를 인스턴스 메서드와 타입 메서드로 동시에 구현할 수 있다. 
이 메서드는 서브클래스로 상속되지만 오버라이딩은 불가하다. 

<pre><code>...
  class func reset() {
  
  }
  ...
</pre></code>
위와 같이 속성과 마찬가지로 static 대신 class 로 선언하면, 오버라이딩을 허용하는 동시에 프로토콜의 요구사항을 충족시킨다.


## Initializer Requirements
프로토콜에서 생성자를 선언할 때는 메서드와 마찬가지로 바디를 생략한 형태로 선언한다. 
그리고 Failable initializer 를 선언하는 것도 가능하다.
<pre><code>protocol ProtocolName {
  init(param)
  init?(param)
  init!(param)
}</pre></code>

### Memberwise initializer
<pre><code>protocol Figure{
  var name: String { get }
  init(name: String)
}

struct Rectangle: Figure {
  var name: String
} 
// 생성자를 구현해야 할 것 같지만, 이 자체로도 프로토콜 요구사항을 충족시킨다. 
  Rectangle 구조체에는 생성자가 적용되어 있지 않다. 이 경우 Memberwise initializer가 자동 적용된다. 
  Memberwise initializer는 속성 이름과 동일한 argument label을 가지고 있는데, 마침 프로토콜에 선언되어 있는 생성자와 동일하다.
  따라서 프로토콜의 요구사항을 충족시키는 것이다. 
</pre></code>


<pre><code>protocol Figure{
  var name: String { get }
  init(n: String) //프로토콜의 argument label이 n 으로 바뀜
}

struct Rectangle: Figure {  //이렇게 하면 더이상 Memberwise initializer를 통해 프로토콜 요구사항을 충족시키지 못한다. 
  var name: String
  
  init (n: String) { //그래서 생성자를 직접 구현한다.
    name = n
  }
} 

class Circle: Figure {
  var name: String
  
  init (n: String) { //Error! 클래스는 상속을 고려해야 하고, 모든 서브클래스에서 프로토콜의 요구사항을 충족시켜야 한다. 따라서 required 키워드를 추가해야 한다. 
    name = n
  }
}
</pre></code>

아래와 같이 required 키워드를 추가하면 에러가 발생하지 않는다. 
이제 Circle class 와 이 클래스를 상속한 모든 서브클래스는 프로토콜 요구사항을 충족해야 한다. 
<pre><code>class Circle: Figure {
  var name: String
  
  required init (n: String) {
    name = n
  }
}
</pre></code>

### 예외! final
<pre><code>final class Triangle: Figure { //더이상 상속되지 않기 때문에, 상속을 고려할 필요가 없다. 
  var name: String
  
  init (n: String) { //따라서 required 키워드 없이도 요구사항을 충족한다. 
    name = n
  }
}</pre></code>

<pre><code>class Oval: Circle, Figure { //Error! 이미 Super class 에서 프로토콜 요구사항을 상속하고 있기 때문에 다시 Figure 를 채용하는 것은 중복이다.
  
}</pre></code>

<pre><code>class Oval: Circle { 
  var prop: Int
  
  init() { //여기에서 구현한 생성자는 프로토콜의 선언되어 있는 생성자와 파라미터 목록이 다르기 때문에 요구사항을 충족하지 못한다. 따라서 프로토콜과 동일한 생성자를 구현해야 한다. 
    prop = 0
    super.init(n: "Oval")
  }
  
  required convenience init(n: String) {
    self.init() //반드시 지정생성자로 구현해야 하는 것은 아니다. 지금처럼 convenience init 으로 구현하더라도 요구사항을 충족할 수 있다. 다만 여기에서도 required 가 필요하다. 
  }
}</pre></code>


### Non-faliable initializer 와 Failable initializer
<pre><code>protocol Grayscale {
  init(white: Double)
}

struct Color: Grayscale {
  init?(white: Double) { //Error! non-failable init 요구사항을 failable init으로 충족시킬 수 없다는 에러가 발생한다.
    
  }
}

struct Color: Grayscale {
  init!(white: Double) { //이렇게 하면 에러는 발생하지 않지만, 초기화에 실패하면 런타임 에러가 발생한다. 
    
  }
}</pre></code>


프로토콜의 생성자를 Failable initializer 로 바꾸어본다.
<pre><code>protocol Grayscale {
  init?(white: Double)
}

struct Color: Grayscale {
  init!(white: Double) { // (1) -- 문제 없다
    
  }
}

struct Color: Grayscale {
  init?(white: Double) { // (2) --문제 없다
    
  }
}

struct Color: Grayscale {
  init(white: Double) { // (3) --문제 없다
    
  }
}</pre></code>

## Subscript requirements
다른 멤버와 마찬가지로 구현 부분은 포함시키지 않는다. 
속성과 마찬가지로 brace 사이에 get, set을 추가하여 가변성을 지정한다. 
<pre><code>protocol ProtocolName {
  subscript(param) -> ReturnType { get set }
}</pre></code>

<pre><code>protocol List {
  subscript(idx: Int) -> Int { get }
}

struct DataStore: List {
  subscript(idx: Int) -> Int {
    return 0
  } // 이와 같이 읽기 전용 서브스크립트를 구현하면 요구사항이 충족된다.
}</pre></code>

### Subscript 에 set keyword 추가
<pre><code>protocol List {
  subscript(idx: Int) -> Int { get set }
}

struct DataStore: List {
  subscript(idx: Int) -> Int {
    return 0
  } // Error!  요구사항을 충족시키려면 읽기, 쓰기가 모두 가능해야 한다. 
}</pre></code>

<pre><code>struct DataStore: List {
  subscript(idx: Int) -> Int {
    get {
      return 0
    }
    set {
      ...
    }
  } // subscript 가 읽기, 쓰기가 모두 가능해지기 때문에 프로토콜의 요구사항을 충족한다. 
}
</pre></code>

subscript 에서 get 으로 선언했다고 하여 실제 구현에서 get 블록만 구현해야 하는 것은 아니다. 
값을 읽을 수 있는 요구사항만 충족시키면 나머지 구현에는 제약이 없다. 
subscript 에서 get 블록을 생략할 수 없는 것 처럼, 프로토콜에서도 get 키워드를 생략할 수 없다.
get 은 필수이고, set 은 필요에 따라 추가할 수 있다. 
<pre><code>protocol List {
  subscript(idx: Int) -> Int { get } //Set 키워드를 제거함
}

struct DataStore: List {
  subscript(idx: Int) -> Int {
    get {
      return 0
    }
    set {
      ...
    }
  } // DataStore 가 이미 요구사항을 충족시키고 있기 때문에 에러가 발생하지 않는다. 
}
</pre></code>
