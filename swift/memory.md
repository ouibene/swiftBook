# Memory

## Bit / Byte
메모리는 0과 1을 저장할 수 있는 반도체이며, 전압 차이를 이용하여 데이터를 저장한다. 전기가 들어오면 1, 그렇지 않으면 0이 저장된다.
0이나 1을 저장할 수 있는 가장 작은 공간을 Bit 라고 한다. 여기에 저장되는 모든 데이터는 종류에 관계 없이 모두 2진수로 변환되어 저장된다.
8개의 Bit 가 모이면 Byte 가 된다. 1Byte에는 얼마만큼의 값을 저장할 수 있을까?
1Bit에는 0과 1을 저장할 수 있다.
2Bit에는 0과 1의 조합 4개를 저장할 수 있다. (2^2)
1Byte에는 2^8 로 256개의 패턴을 저장할 수 있다. 양수만 저장할 수 있다고 한다면 나타낼 수 있는 수는 0부터 255까지를 저장할 수 있다. 
양수와 음수를 모두 저장한다고 한다면 -128 부터 127까지의 값을 저장할 수 있다.

1Byte 메모리에 양수 22를 저장한다면...
00010110 과 같이 저장된다.

### Bit의 구조
가장 왼쪽에 있는 Bit를 최상위비트, MSB(Most Significant Bit)라고 하고
가장 오른쪽에 있는 Bit를 최하위비트, LSB(Least Significant Bit)라고 한다.
실제 데이터를 저장하는 Bit 를 Data Bit 라고 하는데
양수만 저장할 때에는 모든 Bit 를 Data Bit로 사용한다.

### Sign Bit
컴퓨터는 음수를 있는 그대로 저장할 수 없다. 대부분의 컴퓨터는 최상위 비트를 이용하여 음수와 양수를 표현한다.
MSB가 0이면 Positive Number를 의미하고, 1이면 Negative Number를 의미한다.
이런 역할을 하는 Bit를 Sign Bit라고 한다. 
하나의 비트를 부호 비트로 사용하기 때문에, 실제 데이터를 저장하는 데이터 비트가 하나 줄어들게 되고 값을 저장하는 범위가 달라지게 된다.

### 2's Complement
초창기 컴퓨터는 음수를 저장하기 위해 부호비트를 1로 바꾸고 나머지 비트는 양수와 동일한 방식으로 저장했는데 많은 문제가 발생하였다.
그래서 현재는 2의 보수방식을 사용한다.
이 방식은 양수의 Bit 값을 bitwise not 연산을 하고 1을 더하여 음수를 표현한다.
bitwise not 연산은 bit가 1인 경우 0으로 바꾸고, 0인 경우 1로 바꾸는 연산을 의미한다.

00010110 을 2의 보수방식으로 나타낸다면 다음과 같다.
<pre><code>11101001 <- (1) 00010110에 Bitwise not 연산을 한다
11101010 <- (2) (1)에 +1 한다</pre></code>

따라서 -22는 11101010이 된다. 
최상위비트는 마이너스를 나타내는 1이 되고, 나머지 7개 비트는 Data Bit로 사용된다.

### 메모리의 크기
<pre><code>Bit -> 8Bit == Byte -> 1024Byte == Kilobyte -> 1024Kilobyte == Megabyte
1024Megabyte == Gigabyte -> 1024Gigabyte == Terabyte -> 1024Terabyte == Petabyte
1024Petabyte == Exabyte -> 1024Exabyte == Zettabyte -> 1024Zettabyte == Yottabyte</pre></code>

### 메모리 주소
메모리에는 1Byte 를 저장할 수 있는 공간마다 고유한 주소가 할당되어 있다.
CPU는 이 주소를 통해서 메모리에 접근한다. 
CPU는 메모리 주소를 저장하고 특정 위치에 접근하기 위해서 Memory Address Register 를 사용한다. 
주소 레지스터의 크기는 CPU의 Bit수와 같다.
32bit CPU에서는 주소 레지스터를 통해 약 42억개(4,294,967,296)의 레지스터에 접근할 수 있다.
이를 GB로 환산하면 4GB이다. 
32Bit를 사용하는 컴퓨터의 최대 메모리 영역이 4GB인 이유는 이 때문이다.
64Bit CPU는 단순히 두 배가 아니라 이론적으로는 46,744,073,709,551,616개의 레지스터의 접근할 수 있다. 
사용가능한 최대 메모리 영역은 16EB(Exabyte)이다.
실제로는 운영체제의 메모리 관리 방식에 따라서 더 작은 크기로 제한된다.

### 메모리 공간 분류
운영체제는 프로그램을 실행할 때 마다 프로그램이 사용할 메모리 공간을 할당한다.
이 공간은 사용 용도에 따라 크게 아래의 네 가지로 나누어진다.

* *Code*
 기계어로 번역된 프로그램 코드가 저장된다.
* *Data*
 정적 변수와 전역 변수가 저장된다. 여기에 저장된 데이터는 프로그램이 실행될 때 생성되었다가 프로그램이 종료되면 제거된다. 
* *Heap*
 동적으로 할당된 데이터가 저장된다. 다른 영역은 할당 할 공간의 영역을 예측할 수 있지만, 
 Heap은 동적으로 할당되기 때문에 이 영역을 예측할 수 없다. 
 여기에 저장된 데이터는 생성과 제거시점이 정해져있지 않다. 따라서 코드를 통해 직접 생성 및 제거해주어야 한다.
 더 이상 필요하지 않는 데이터를 제거하지 않고 그대로 둔다면, 프로그램이 종료될 때 까지 유지되고 이런 메모리가 많아지면 결국 메모리가 부족해진다.
 이를 Memory Leaks 라 한다.
* *Stack*
 지역변수, 파라미터, 리턴 값 등이 저장된다. 
 스택은 함수 호출과 밀접한 관련이 있다.
 함수를 호출하면 함수에서 사용하는 모든 값을 저장하는 메모리 공간이 생성된다. 이 공간을 Stack frame이라고 한다.
 Stack frame은 함수 실행이 종료되면 스택에서 제거되고 다른 함수에서 메모리를 사용할 수 있게 된다.
 스택은 Last in first out 방식을 따른다. 함수가 실행 될 때 마다 새로운 스택 프레임이 되고, 함수 실행이 종료되면 가장 최근에 실행 된 스택 프레임부터 제거된다.
 
값 형식(Value Type)은 Stack에 저장된다. 더이상 사용되지 않는 경우 자동으로 제거되기 때문에 메모리 관리가 상대적으로 쉽다.
반면 참조 형식(Reference Type)은 실제 값을 Heap에 저장하고 힙 메모리 주소를 스택에 저장한다. 실제 값에 접근할 때는 항상 스택에 있는 주소를 통해 접근한다. 
값을 더 이상 사용하지 않는다면 두 공간에 저장되어 있는 데이터를 완전히 삭제하여 메모리 누수를 방지하는 것이 중요하다. 
 

## Value Type & Reference Type
Swift 에서 Structure, Enumeration, Tuple은 값 형식(Value Type)으로 분류하고, 
Class, Closure는 참조 형식(Reference Type)으로 분류한다. 

<pre><code>struct SizeValue {
 var width = 0.0
 var height = 0.0
}

/* 인스턴스 생성.
 * 스택에 메모리 공간이 생성되고 여기에 0.0 으로 초기화된 값이 저장된다.
 * 그리고 value 변수와 memory 공간이 연결된다. 
 */

var value = SizeValue()

/* 값 복사.
 * value instance의 복사본이 새로운 메모리에 저장되고 
 * 이 메모리와 value2 변수가 연결된다. 
 * 두 인스턴스의 속성 값은 모두 동일하지만
 * 서로 다른 메모리에 저장된 개별 인스턴스이다.
 */ 
var value2 = value

/* 새로운 값으로 업데이트
 * value2와 연결된 메모리가 새로운 값으로 업데이트 된다.
 * 그러나 value 와 연결된 메모리는 업데이트되지 않는다. 
 */
value2.width = 1.0
value2.height = 2.0

value
value2 //이 코드를 통해 값을 확인해보면 value2만 업데이트 됨을 알 수 있다. 

값 형식은 항상 stack에 저장된다. 그리고 값을 전달할 때 마다 새로운 복사본이 생성된다. 
스위프트에서는 값을 변경하는 경우에만 실제로 복사되도록 최적화 하고 있다. (Copy on write)
</pre></code>

<pre><code>class SizeValue {
 var width = 0.0
 var height = 0.0
}

/* 인스턴스 생성 
 * 스택과 힙에 새로운 공간이 생성된다.
 * 힙에는 인스턴스가 저장되고 스택에는 힙 메모리의 주소가 저장된다. 
 * 그리고 object 변수는 스택에 생성된 메모리와 연결된다.
 * 값 형식 처럼 바로 인스턴스에 접근할 수 없고, 스택을 거쳐 접근한다.
 */
var object = SizeObject()

/* 스택에 새로운 메모리가 생성되고
 * 스택에 저장했던 주소가 그대로 복사된다. 
 */
var object2 = object

/* object2를 변경하였지만
 * object 또한 변경된다.
 * 둘은 같은 인스턴스를 바라보기 때문이다.
 */
object2.width = 1.0
object2.height = 2.0

/* 인스턴스에 저장된 값을 확인해본다 */
object
object2 //모든 인스턴스에 동일한 값이 저장되어 있다.
</pre></code>

## ARC(Automatic Reference Counting)
스택에 저장된 데이터는 자동으로 관리되기 때문에 관리 할 필요 없다. 반면 힙에 저장된 데이터는 필요하지 않은 시점에 직접 제거해야 한다.
메모리 관리 모델은 힙에 저장되는 데이터를 관리한다. 클래스 인스턴스의 메모리를 관리한다. 
이 파트에서 말하는 인스턴스는 모두 클래스 인스턴스를 말한다. 

### Swift의 메모리 관리 모델
스위프트는 objective-c와 동일한 메모리 관리 모델을 사용한다.
언어에 속한 메모리 관리 모델은 아니고, 애플이 제공하는 개발환경인 cocoa에서 사용하는 관리모델이다.
cocoa에서 사용하는 관리 모델은 두개이다. objective-c는 두 개를 모두 사용할 수 있고, swift는 ARC모델만 지원한다.

Objective-C
- MRC
- ARC

Swift
- ARC

### Ownership Policy 와 Reference Count
두 모델을 이해하기 위해서는 소유정책(Ownership Policy)과 참조 카운트(Reference Count)에 대하여 이해해야 한다.
인스턴스는 하나 이상의 소유자가 있는 경우 메모리에 유지되고, 반대로 소유자가 없다면 메모리에서 제거 된다.
제거 시점을 파악하기 위해서 소유자 수를 저장하는데 이를 참조 카운트라고 한다. 
인스턴스는 참조카운트가 1 이상이면 메모리에 유지되고, 0이 되면 메모리에서 제거되는 것이다.

클래스 인스턴스를 변수에 저장하면 변수가 소유자가 된다. 이 시점의 인스턴스의 참조카운트는 1이다.
또다른 변수가 동일한 인스턴스를 소유하면 참조 카운트는 2가 된다. 
인스턴스를 소유하기 위해서는 특별한 메시지를 전달해야 한다. 코드레벨에서 보자면 인스턴스가 제공하는 retain 변수들을 호출하는 것과 같다. 
인스턴스가 더이상 필요하지 않으면 소유권을 포기해야 한다. 소유권 획득과 마찬가지로 특별한 메시지를 전달하는데, 이를 release method라 한다.
소유자가 release method를 호출하여 소유권을 포기하면 인스턴스의 참조 카운트가 1 감소한다. 
(이 retain, release method는 컴파일러가 자동으로 컴파일 시에 추가하는 코드이므로 유저가 호출할 필요가 없다. )
나머지 소유자가 소유권을 포기하면 마찬가지로 참조 카운트가 1 감소한다. 
참조 카운트가 0이 되면 즉시 메모리에서 제거된다.

### MRC(Manual Reference Counting)
MRC에서는 소유정책과 관련된 코드를 직접 구현해야 한다. 그러나 소유권을 제대로 처리하는 것이 어렵다. 
ARC에 비해 많은 코드를 작성해야 하며, 메모리 오류가 발생할 가능성도 높다. 따라서 프로그램의 안정성이 낮아지고 디버깅이 어려워진다.

### ARC(Automatic Reference Counting)
소유정책을 자동으로 처리한다. 소유정책과 참조 카운트를 처리하는 방식은 MRC와 동일하다.
그러나 컴파일러가 메모리 관리 코드를 자동으로 추가하기 때문에 관련 코드를 직접 작성할 필요가 없다. 
작성해야 하는 코드의 양이 줄고 프로그램의 안정성이 증가한다. 
Swift 는 ARC를 기본 모델로 사용한다. 

Objective-C는 두 모델 모두를 지원하지만 특별한 이유가 없다면 ARC를 사용한다.

### ARC가 사용하는 세 가지 참조
#### Strong Reference
기본적으로 인스턴스와 소유자는 강한 참조로 연결된다. 대상을 소유할 때 마다 참조 카운트가 1씩 증가하고, 소유권을 포기할 때 마다 1씩 감소한다.
인스턴스는 소유자가 존재하는 동안에는 메모리에서 제거되지 않는다. 

<pre><code>class Person {
 var name = "John Doe"
 
 deinit {
  print("person deinit") //인스턴스가 제거되기 직전에 자동으로 호출된다. (소멸자)
 }
}

var person1: Person?
var person2: Person?
var person3: Person?

person1 = Person() //person1 변수와 인스턴스가 강한 참조로 연결되고 인스턴스의 참조 카운트는 1 증가한다.
person2 = person1
person3 = person1 // 2, 3의 두 변수도 인스턴스와 강한 참조로 연결되며, 참조 카운트도 1씩 두 번 증가하여 3이 된다. 

person1 = nil //nil 을 저장하는 것은 소유권을 포기하는 것과 같다. 소유권을 포기하면 즉시 강한 참조가 제거되고 참조 카운트도 1 감소한다.
person2 = nil //이 코드 이후 참조 카운트가 1이 된다. 여전히 참조 카운트가 1이기 때문에 인스턴스가 메모리에서 제거되지 않는다.

person3 = nil //마지막 소유자가 소유권을 포기하면 참조 카운트가 0이 되고, 인스턴스는 이 시점에 메모리에서 제거된다. 이 때 소멸자가 호출된다. 
</pre></code>


## 강한 참조 사이클(Strong reference cycle)
<pre><code>class Person {
    var name = "John Doe"
    var car: Car?
 
    deinit {
     print("person deinit")
    }
}

class Car {
    var model: String
    var lessee: Person?
    
    init(model: String) {
        self.model = model
    }
    
    deinit {
        print("car deinit")
    }
}

var person: Person? = Person()  //Person 인스턴스는 person 변수와 강한 참조로 연결된다. 
var rentedCar: Car? = Car(model: "Porsche") //Car 인스턴스는 rentedCar 변수와 강한 참조로 연결된다.

// 이 시점의 두 인스턴스의 참조 카운트는 모두 1이다.

person?.car = rentedCar //car 속성이 rentedCar 변수에 저장된 인스턴스를 소유하게 된다. 속성과 인스턴스가 강한 참조로 연결되고 Car 인스턴스의 참조 카운트는 2가 된다. 
rentedCar?.lessee = person //lessee 속성과 Person 인스턴스가 강한 참조로 연결되고, Person 인스턴스의 참조카운트는 2가 된다. 

person = nil //소유권을 포기한다. Person 인스턴스의 참조 카운트가 1 감소한다. Person 인스턴스의 참조 카운트는 여전히 0보다 크기 때문에 메모리에서 제거되지는 않는다.
rentedCar = nil //Car instance의 참조 카운트도 1 감소한다. 그러나 Car 인스턴스의 참조 카운트는 0보다 크다(1이다.) 따라서 메모리에서 제거되지 않는다. 
</pre></code>

문제점은 두 인스턴스가 속성을 통해서 서로를 소유하고 있다는 점이다.
person 변수와 rentedCar 변수에 nil을 저장해서 더이상 인스턴스에 접근할 수단이 없다. 
그래서 두 인스턴스를 정상적으로 해제할 방법이 없다.
nil 을 다시 할당한다고 해도 불가능하다. 왜냐하면 인스턴스가 제거되었기 때문이다. 
이와 같이 강한 참조 떄문에 인스턴스를 정상적으로 해제할 수 없는 문제를 *강한 참조 사이클*이라고 한다.
ARC는 메모리를 대신 처리해주지만, 참조 사이클까지 자동으로 처리하지는 못한다. 

강한 참조 사이클은 약한 참조(Weak reference)와 비소유 참조(Unowned Reference)를 통해 해결한다. 
두가지 방식 모두 인스턴스 사이의 강한 참조를 제거하는 방식으로 문제를 해결한다. 
강한 참조와는 달리 참조 카운트를 증가시키거나 감소시키지 않는다. 따라서 참조를 통해 인스턴스에 접근할 수는 있지만, 인스턴스가 사라지지 않도록 유지하는 것은 불가능하다. 

#### Weak Reference
인스턴스를 참조하지만 소유하지는 않는다. 참조카운트도 변하지 않는다. 
그래서 대상 인스턴스를 참조하고 있는 동안, 대상 인스턴스는 언제든 사라질 수 있다. 
이런 특징 때문에 소유자에 비해 짧은 생명주기를 가진 인스턴스를 참조할 때 주로 사용한다. 
약한 참조는 항상 옵서널 형식으로 선언한다. 
그리고 var 키워드 앞에 weak 키워드를 추가한다.

<pre><code>weak var name: Type?</pre></code>

참조하고 있는 인스턴스가 해제되면 자동으로 nil로 초기화 된다. 

<pre><code>class Person{
    var name = "John Doe"
    var car: Car?
 
    deinit {
     print("person deinit")
    }
}

class Car {
    var model: String
    weak var lessee: Person? //weak 키워드를 추가한다. 그러면 속성이 약한 참조로 선언되고 Person인스턴스를 참조하지만 소유하지는 않게 된다. 그래서 강한 참조 사이클이 제거된다.
    
    init(model: String) {
        self.model = model
    }
    
    deinit {
        print("car deinit")
    }
}

var person: Person? = Person()  
var rentedCar: Car? = Car(model: "Porsche")

person?.car = rentedCar //여기까지는 이전 코드와 동일하다.
rentedCar?.lessee = person //lessee는 약한 참조로 선언되어 있어서 Person 인스턴스의 인스턴스 카운트를 증가시키지 않는다. 

person = nil //참조 카운트가 0이 되면서 인스턴스가 메모리에서 제거된다. 이 때 car 속성이 제거되면서 car instance에 대한 소유권을 자동으로 포기한다. 
Car instance 의 참조 카운트가 1 감소하고 lessee 속성은 nil 로 초기화 된다. (약한 참조는 참조하는 인스턴스가 해제되면 자동으로 nil로 초기화 된다.)
약한 참조를 통해 강한 참조 사이클이 제거 되었고, Person인스턴스에서 소멸자가 실행된 후 메모리에서 제거된다. 

rentedCar = nil //Car instance의 참조카운트가 0이 되고, 메모리에서 제거된다. 
</pre></code>

모든 소멸자가 호출됨을 위 코드로 확인할 수 있을 것이다. 

#### Unowned Reference
비소유 참조는 약한참조와 동일한 방식으로 강한 참조 사이클을 해결한다.
그러나 optional 형식이 아니라 non-optional 형식이다. 
참조 사이클을 해결하면서 속성을 non-optional로 선언해야 할 때 주로 사용된다. 
그리고 인스턴스의 생명주기가 소유자와 같거나 더 긴 경우에 주로 사용된다. 

비소유 참조는 unowned 키워드를 통해 선언한다. 그리고 약한 참조와는 달리 non-optional 형식으로 선언한다. 
optional 형식이 아니기 때문에 참조하고 있는 인스턴스가 해제되어도 nil로 초기화 되지 않는다.
그래서 해제된 인스턴스에 접근할 경우 런타임 에러가 발생한다. 
<pre><code>unowned var name: Type</pre></code>

<pre><code>class Person{
    var name = "John Doe"
    var car: Car?
 
    deinit {
     print("person deinit")
    }
}

class Car {
    var model: String
    unowned var lessee: Person //unowned 키워드를 추가한다
    
    init(model: String, lessee: Person) { //생성자를 수정하였다
        self.model = model
        self.lessee = lessee
    }
    
    deinit {
        print("car deinit")
    }
}

var person: Person? = Person()  
var rentedCar: Car? = Car(model: "Porsche", lessee: person!)

person?.car = rentedCar
이렇게 하면 두 인스턴스가 서로를 참조하게 된다.

person = nil
rentedCar = nil
모든 인스턴스가 정상적으로 해지됨을 볼 수 있다
</pre></code>


## Closure capture list
클로저(Closure) 에서도 참조 사이클이 발생할 수 있다. 
클로저가 인스턴스를 캡처하고, 인스턴스는 클로저를 강한 참조로 소유하는 상황에서 발생한다. 
이 경우에도 약한 참조와 비소유 참조를 통해 해결한다. 

<pre><code>class Car {
    var totalDrivingDistance = 0.0
    var totalUsedGas = 0.0
    
    lazy var gasMileage: () -> Double = { //지연 저장 속성으로 선언되어 있고, Double를 리턴하는 클로저가 저장되어 있다. 
        return self.totalDrivingDistance / self.totalUsedGas 
        /* 클로저는 self expression 을 이용하여 인스턴스 속성에 접근하고 있다. 
        self는 인스턴스 자체를 나타내는 특별한 속성이다. 이 속성을 클로저에서 사용하면 self가 나타내는 인스턴스가 캡처된다.
        클로저는 실행이 종료될 때 까지 self를 강한 참조로 캡처한다. 그래서 self가 나타내는 인스턴스는 클로저의 실행이 완료될 떄 까지 메모리에서 제거되지 않는다.
        인스턴스를 캡처하는 클로저는 인스턴스 속성에 저장되어 있다. 속성이 약한 참조 또는 비소유 참조로 선언되어있지 않기 때문에 
        인스턴스는 속성에 저장된 클로저를 강하게 참조한다. 결과적으로 클로저와 인스턴스는 강한 참조로 연결된다. */
    }
    
    func drive() {
        self.totalDrivingDistance = 1200.0
        self.totalUsedGas = 73.0
    }
    
    deinit { 
        print("car deinit")
    }
}

var myCar1: Car? = Car()
myCar1?.drive() //아직 클로저가 실행되지 않았고 강한 참조 사이클도 발생하지 않는다.
myCar1 = nil //deinit 디버그 메시지가 출력된다. (인스턴스가 정상적으로 해지됨)

var myCar2: Car? = Car()
myCar2?.gasMileage() //이 시점에 클로저가 실행되고 self(myCar2 instance)를 소유하고 강한 참조 사이클이 발생한다. 
myCar2 = nil //소멸자가 호출되지 않는다. 강한 참조 사이클 때문에 인스턴스가 정상적으로 해지되지 않았기 때문이다.
</pre></code>

## Closure capture list 의 문법
클로저 캡처 리스트는 클로저 문법에서 parameters 앞에 온다. 그리고 Squre braket 사이에 캡처 할 대상을 콤마로 구분하여 나열한다. 
<pre><code>{ [list] (parameters) -> ReturnType in
    Code
}</pre></code>

클로저에서 문법 최적화를 통해 parameters와 return type을 생략할 때에는 in 키워드를 생략할 수 있으나, 
클로저 캡처 리스트를 사용할 때에는 in 키워드를 생략할 수 없고 list 가 in 키워드의 앞에 위치한다. 클로저 바디와 구분하기 위해 반드시 필요하다.
<pre><code>{ [list] in
    Code
}</pre></code>

클로저 캡처 리스트는 세 가지 형태로 작성한다.

### Value Type
값 형식을 캡처할 떄에는 대상의 이름만 표기한다. 
<pre><code>{ [valueName] in
    Code
}</pre></code>

#### 예제
이 코드에서 c 상수에 저장된 클로저는 a, b 두 변수를 캡처한다.
클로저가 값을 캡처할 때는 복사본이 아닌 참조가 저장된다.
변수의 값을 바꾼 다음 클로저를 실행해보자.
<pre><code>
var a = 0
var b = 0
let c = { print(a, b) }

a = 1
b = 2
c()    //1 2가 출력됨
</pre></code>

이번에는 a 변수를 클로저 캡처 리스트에 추가한다. 스퀘어 브라켓 사이에 대상의 이름을 나열한다.
<pre><code>
var a = 0
var b = 0
let c = { [a] in print(a, b) }

a = 1
b = 2
c()    //a의 값은 캡처 시점에 저장되어 있는 값으로 출력된다. (0 2 가 출력 됨)
</pre></code>
값 형식을 클로저 캡처 리스트에 추가하면 참조 대신 복사본을 캡처한다. 

### Reference Type
참조 형식을 캡처할 때는 반드시 대상 앞에 weak 키워드나 unowned 키워드가 있어야 한다. 
weak 키워드는 약한 참조로 캡처하고, unowned 는 비소유 참조로 캡처한다. 
<pre><code>{ [weak instanceName, unowned instanceName] in
    statements
}</pre></code>

클로저 캡처 리스트를 사용하여 이전의 코드에서 발생했던 참조 사이클 문제를 해결해보자.
#### weak reference 를 통한 참조 사이클 해결
<pre><code>class Car {
    var totalDrivingDistance = 0.0
    var totalUsedGas = 0.0
    
    lazy var gasMileage: () -> Double = { [weak self] in        //약한 참조
        guard let strongSelf = self else {return 0.0}
        return strongSelf.totalDrivingDistance / strongSelf.totalUsedGas 
        /* 약한 참조는 옵셔널 형식이다. 그래서 self에 접근할 때 unwrapping 하거나 optional chaining 으로 멤버에 접근해야 한다.
           클로저의 실행이 완료되지 않은 시점에 캡처 대상이 해제될 수 있다면 주로 약한 참조를 사용한다. 
           만약 car instance 가 해지된 직후 클로저가 호출되었다면 self는 nil 이 된다. 
           그래서 옵셔널 바인딩이 실패하고 코드가 종료된다.
           약한 참조로 캡처할 때는 캡처 대상이 해제된 경우도 함께 고려해야 한다.
           클로저에서 인스턴스를 약한 참조로 캡처하고 있기 때문에 더이상 강한 참조 사이클은 발생하지 않는다. */
    }
    
    func drive() {
        self.totalDrivingDistance = 1200.0
        self.totalUsedGas = 73.0
    }
    
    deinit { 
        print("car deinit")
    }
}

var myCar: Car? = Car()
myCar?.gasMileage() 
myCar = nil //소멸자가 호출된다.
</pre></code>

#### unowned reference 를 통한 참조 사이클 해결
<pre><code>class Car {
    var totalDrivingDistance = 0.0
    var totalUsedGas = 0.0
    
    lazy var gasMileage: () -> Double = { [unowned self] in        //비소유 참조. 옵셔널 형식이 아니기 때문에 옵셔널 바인딩이나 옵셔널 체이닝은 필요 없다. 
        return self.totalDrivingDistance / self.totalUsedGas 
        /* 클로저 캡처 리스트를 사용하지 않은 코드와 동일한 방식으로 속성에 접근하고 있다.
           그러나 여기에서 사용된 self는 비소유 참조로 캡처되었기 때문에 더이상 강한 참조 사이클 문제를 발생시키지 않는다. */ 
    }
    
    func drive() {
        self.totalDrivingDistance = 1200.0
        self.totalUsedGas = 73.0
    }
    
    deinit { 
        print("car deinit")
    }
}

var myCar: Car? = Car()
myCar?.gasMileage() 
myCar = nil //소멸자가 호출된다.
</pre></code>

비소유 참조로 캡처한 대상은 클로저 실행이 종료되기 전에 해지될 수 있다.
해지된 대상에 접근하는 경우 런타임 오류가 발생하기 때문에 유의한다.
그래서 비소유 참조는 캡처 대상의 생명 주기가 클로저와 같거나 더 긴 경우에 주로 사용한다. 
