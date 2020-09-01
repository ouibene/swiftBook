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
