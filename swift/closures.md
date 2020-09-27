# Closures
클로저는 비교적 짧고 독립적인 코드조각이다. 
Objective-C에서는 block이라고 부르고, java에서는 lambda라고 부르기도 한다.
Swift 에서 Closure에 해당하는 것은 세 가지이다. 

Swift 의 함수를 굳이 구분하자면 Global function, Nested function 이 두 가지로 구분할 수 있다. 
그리고 함수는 이름을 가지고 있다. 이것들은 클로저 중에서도 이름을 가진 클로저이다. 
나머지 하나는 익명 함수인데, 이름이 없는 함수라는 뜻이다. 그리고 익명 함수는 이름이 없는 클로저이다.

함수는 First class citizen이다. 그리고 클로저도 마찬가지이다. 
함수처럼 변수나 상수에 저장할 수 있고, 파라미터로 전달할 수 있고, 함수와 메서드에서 리턴할 수도 있다.
이렇게 하려면 자료형이 필요한데, 자료형을 표현하는 문법은 Function 타입과 완전히 동일하다.
이 말을 다시 말하자면, 함수와 클로저는 서로 호환된다고도 할 수 있다. 

### Closures 문법
파라미터와 리턴형을 표현하는 것은 function과 똑같다. 
차이점은 다음과 같다.
1. 앞 부분에 func 키워드가 없다
2. 모든 요소가 brace 내부에 포함된다
3. 파라미터부터 리턴형까지 Closure head 라고 부르는데, closure head 와 body 를 in 키워드로 구분한다.
함수와 마찬가지로 파라미터와 리턴형을 생략할 수 있는데 이 때 in 키워드도 생략된다. 
```
{ (parameters) -> ReturnType in
  statements
}
```
아래는 가장 단순한 형태의 클로저이다.
```
{ statements }
```

문자열을 출력하는 가장 단순한 클로저를 만들어보자.
```
{ print("Hello, Swift") }
```
컴파일 에러가 발생하지만 이는 클로저가 global scope 에서 단독으로 작성할 수 없기 때문에 발생하는 에러이다.

아래와 같이 상수에 저장하면 에러가 사라진다.
그리고 아래 클로저는 앞서 말했던, 파라미터와 리턴형이 생략된 클로저이다. 
```
let c = { print("Hello, Swift") }
```
그렇다면 클로저의 자료형은 무엇일까?
출력해보면 ()->() 이다. 이를 글로 표현하자면 파라미터가 없고 리턴형이 없는 클로저라고 한다.
자료형이 동일하기 때문에 함수와 호환이 된다. 함수를 전달하는 곳에 클로저를 전달하고, 클로저를 전달할 곳에 함수를 전달하는 것도 가능하다.

지금 c 상수에는 실행할 코드가 클로저로 저장되어 있다.
클로저는 이름이 없는 함수인데, c라는 이름을 붙인 것과 같다.
아래 코드를 실행하면...
```
c()
```
Hello, Swift 라는 문자열이 출력된다.

이번에는 클로저에 파라미터와 리턴형을 추가해보자.
```
let c2 = { (str: String) -> String in 
  return "Hello, \(str)"
}
```
클로저와 함수의 문법은 동일하지만 한가지 차이가 있다.
클로저는 Argument label을 사용하지 않는다는 것이다. 
위 코드를 함수라고 가정해보자.
str는 파라미터 이름인 동시에 argument label 이다. 
원래대로 클로저라고 생각하자.
str은 파라미터 이름이고, argument label은 아니다. 
그래서 클로저를 호출할 때는 argument label을 사용하지는 않는다.

위 클로저를 출력해보자.
```
let result = c2("Closure")
print(result)
```
Hello, Closure 라는 문자열이 출력된다.
위 코드에서는 argument label 을 사용하지 않는다. 클로저이기 때문이다.

이번에는 closure 를 parameter 로 전달하도록 하자.
코드의 가독성을 높이기 위하여 type alias 를 하나 선언한다.
```
typealias SimpleStringClosure = (String) -> String

func perform(closure: SimpelStringClosure) {
  print(closure("iOS"))
}

perform(closure: c2)
```
실행하면 Hello, iOS 가 출력된다.
코드를 따라가보자.

perform함수를 호출하면서 argument 로 전달했던 c2 가 perform의 파라미터로 전달된다.
함수 바디에서 전달된 closure 를 호출하고, iOS를 파라미터로 전달한다. 
c2 에 저장된 클로저를 보면, 파라미터로 전달된 문자열 앞에 Hello를 붙인 후 문자열을 리턴하고 있다.
다시 perform 함수에서 closure 가 리턴한 문자열을 print 한다. 
그래서 Hello, iOS 가 콘솔에 출력되는 것이다. 
