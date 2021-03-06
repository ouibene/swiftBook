# Extension

익스텐션은 형식 확장에 사용된다. 형식 확장이란 속성, 메서드, 생성자 같은 것(멤버)들을 형식에 추가하는 것을 의미한다.
익스텐션으로 확장할 수 있는 대상은 Class, Structure, Enumeration, Protocol
형식 선언에 새로운 멤버를 추가하는 것이 아니라, 새로운 멤버는 별도의 코드로 구현하고 형식과 연결시켜 기존 멤버와 함께 사용한다.
형식 선언이 포함되어 있는 코드를 수정할 수 없는 경우에도 형식 확장은 가능하다. (ex. 기본 자료형에도 새로운 멤버를 추가할 수 있다.)

익스텐션으로 멤버를 추가하는 것은 가능하지만, 오버라이딩 하는 것은 불가능하다.
오버라이딩이 필요하다면 상속을 통해 서브클래싱 한다.

## Extension 사용법
<pre><code> extension Type {
   ...
 }
</code></pre>

Type 부분에 기존에 존재하는 형식의 이름을 둔다.
형식에 추가할 멤버는 brace 사이에 두고, 이 멤버들은 원래 형식에서 선언된 것 처럼 사용할 수 있다.

### 추가할 수 있는 멤버
* 속성은 계산 속성으로 제한
* 저장 속성이나 프로퍼티 옵저버를 추가하는 것은 불가능
* 메서드는 인스턴스 메서드, 타입 메서드 모두 추가 가능
* 생성자 (클래스를 확장하는 경우에는 간편 생성자만 추가 가능)
* 지정 생성자와 소멸자는 반드시 원본 형식으로 구현해야 한다 (새로운 지정 생성자나 소멸자를 추가하고 싶다면 서브클래싱)
* 익스텐션으로 생성자를 추가할 때는 생성자 델리게이션이 정상적으로 실행되는 것이 중요
* 서브스크립트 추가 
* NestedType 추가 가능

<pre><code> extension Type: Protocol, ... {
   ...
 } </code></pre>

기존 형식에 프로토콜 구현을 추가할 떄 사용한다.


## 예시
<pre><code>struct Size {
  var width = 0.0
  var height = 0.0
}

extension Size {
  var area: Double {
    return width * height
  }
}

let s = Size()
s.width
s.height
s.area //extension 으로 속성을 추가했지만 다른 속성들과 차이가 없다. 
</code></pre>

원본 형식을 수정할 수 있다면 익스텐션을 사용할 필요는 없다.
그러나 원본 형식을 수정할 수 없다면 익스텐션으로 프로토콜 구현을 추가한다.
아래 코드처럼 익스텐션을 이용하여 프로토콜을 구현할 수 있다는 점을 알아두자.

<pre><code>extension Size: Equatable {
  public static func == (lhs: Size, rhs: Size) -> Bool {
    return lhs.width == rhs.width && lhs.height == rhs.height
  }
}</code></pre>


## Properties 추가
익스텐션에서는 계산속성만 추가 가능하다.
앞에서 언급하였듯 저장 속성이나 프로퍼티 옵저버를 추가하는 것은 불가하다.
형식에 존재하는 속성을 오버라이딩 하는 것도 불가하다. 

### 
<pre><code>extension Date {
   var year: Int {
      let cal = Calendar.current
      return cal.component(.year, from: self)
   }
}

let today = Date()
today.year //연도가 return 된다.
</pre></code>


<pre><code>extension Double {
   var radianValue: Double {
      return (Double.pi * self) / 180.0
   }
   
   var degreeValue: Double {
      return self * 180.0 / Double.pi
   }
}

let dv = 45.0
dv.radianValue //45 를 radian으로 변환한 형식이 리턴된다
dv.radianValue.degreeValue //이렇게 연달아 사용할 수도 있다. 이 때에는 다시 degree로 변환된 값이 출력된다.
</pre></code>

## Method 추가

<pre><code>/* double 에 저장된 실수를 섭씨 온도와 화씨 온도로 변환하는 코드 */
extension Double {
   /* Instance method */
   func toFahrenheit() -> Double {
      return self * 9 / 5 + 32
   }
   
   func toCelsius() -> Double {
      return (self - 32) * 5 / 9
   }
   
   /* Type method */
   static func convertToFahrenheit(from celsius: Double) -> Double {
      return celsius.toFahrenheit()
   }
}

let c = 30.0
c.toFahrenheit() //화씨 온도로 변환된 값이 출력된다.

Double.convertToFahrenheit(from: 30.0) //extension을 통해 method 를 추가했지만 원본형식의 Type method와 차이가 없다.</pre></code>


<pre><code>extension Date {
   func toString(format: String = "yyyyMMdd") -> String {
      let privateFormatter = DateFormatter()
      privateFormatter.dateFormat = format
      return privateFormatter.string(from: self)
   }
}

let today = Date()
today.toString() //parameter 생략하고 호출하면 기본값으로 지정된 형식으로 출력 됨

/* 전달된 포맷으로 문자열 출력 됨 */
today.toString(format: "MM/dd/yyyy")</pre></code>


<pre><code>extension String {
   static func random(length: Int, charactersIn chars: String = "abcdefghijklmnopqrstuvwxyz") -> String {
      var randomString = String()
      randomString.reserveCapacity(length)
      
      for _ in 0 ..< length {
         guard let char = chars.randomElement() else {
            continue
         }
         
         randomString.append(char)
      }
      
      return randomString
   }
}

String.random(length: 5) //5자리의 random 문자열 구성</pre></code>


## Initializers 추가

<pre><code>extension Date {
   init?(year: Int, month: Int, day: Int) {
      let cal = Calendar.current
      let comp = DateComponents()
      comp.year = year
      comp.month = month
      comp.day = day
      
      guard let date = cal.date(from: comp) else {
         return nil
      }
      
      self = date
   }
}

Date(year: 2020, month: 8, day: 31)</pre></code>



<pre><code>extension UIColor {
   convenience init(red: Int, green: Int, blue: Int) {
      self.init(red: CGFloat(red) / 255, green: CGFloat(green) / 255, blue: CGFloat(blue) / 255, alpha: 1.0)
   }
}

UIColor(red: 0, green: 0, blue: 255)</pre></code>



### default initializer , Memberwise initializer

<pre><code>struct Size {
   var width = 0.0
   var height = 0.0
   
   init(value: Double) {
      width = value
      height = value
   }
}

위와 같이 생성자 추가 시 아래의 코드에서 에러 발생한다.
아래의 코드에서 에러를 발생시키지 않으려면 기본생성자, 파라미터가 2개인 생성자를 추가하거나...
Size()
Size(width: 12, height: 34)

아래와 같이 Extension 추가한다. Extension을 사용하면 기본으로 제공되는 생성자와 함께 사용할 수 있다. 
extension Size {
   init(value: Double) {
      width = value
      height = value
   }
}

Size()
Size(width: 12, height: 34)</pre></code>


## Subscript 추가
String 기본 구현은 정수 인덱스를 subscript로 전달할 수 없고 sting.index를 전달해야 한다.
아래는 정수 인덱스를 사용할 수 있도록 구현한 예제이다.

<pre><code> extension String {
   subscript(idx: Int) -> String? {
      guard (0.. < count).contains(idx) else {
         return nil
      }
      
      let target = index(startIndex, offsetBy: idx)
      return String(self[target])
   }
}

let str = "Swift"
str[1] //w 가 전달된다.
str[100] //nil 이 전달된다.</pre></code>

<pre><code> extension Date {
   subscript(component: Calendar.Component) -> Int? {
      let cal = Calendar.current
      return cal.component(component, from: self)
   }
}

let today = Date()
today[.year] //2020이 출력된다. (현재 연도)
today[.month]
today[.day] //오늘의 월, 일이 리턴된다. </pre></code>
