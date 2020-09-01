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
