# Error Handling

### Compile Time Error & Runtime Error
에러는 컴파일 타임 에러와 런타임 에러가 있다. 
컴파일 타임 에러는 대부분 문법과 관련이 있다. 컴파일러가 제공하는 정보를 통해 비교적 쉽게 수정 가능하고, Fix it으로 수정 가능하다.
런타임 에러는 프로그램이 실행되는 동안 발생한다. 문법적인 에러가 아니더라도 디바이스나 리소스 상태에 따라 에러가 발생할 수 있다.
보통 런타임 에러가 발생하면 프로그램이 강제적으로 종료된다. 
그러나 발생 가능한 에러를 미리 처리해 두면 프로그램을 크래시 없이 계속 사용할 수 있다. 
강제 종료 횟수가 줄어들면 그만큼 사용성이 증가한다.

### throw expression
```
func name(parameters) throws -> ReturnType {
    statements
}

init(parameters) throws {
    statements
}

{ (parameters) throws -> ReturnType in
    statements
}
```

에러 형식은 특별한 것이 아니다. 아래와 같이 에러 프로토콜을 채용하면 에러 형식이 된다.
그리고 대부분 열거형으로 선언한다. 
에러 프로토콜에는 필수 멤버가 선언되어 있지 않아서, 프로토콜을 채용하겠다고 선언하는 것으로도 충분하다.
```
enum DataParsingError: Error {
    
}
```

아래는 발생 가능한 에러를 몇 가지, case를 통해 선언하는 예제이다.
```
enum DataParsingError: Error {
    case invalidType
    case invalidField
    case missingRequiredField(String)
}
```

일반 형식과 에러 형식의 차이는 하나이다.
에러 형식은 스위프트 에러 처리 시스템에 통합된다. 

### throw 와 throws
에러를 전달하는 것을 에러를 던진다(throw)고 한다. 
throw 키워드 뒤에는 표현식을 사용하는데, 여기에는 에러형식의 인스턴스가 위치한다. 일반 형식의 인스턴스를 사용하면 컴파일 에러가 발생한다. 
throw문은 아무데서나 호출할 수 없고, 코드 블럭이 에러를 던질 수 있다고 선언되어 있어야 한다. 이런 코드블럭은 어떻게 선언하는가?
#### throw
```
throw expression //이 문법에서의 throw 는 에러를 던지는 키워드이다. 
```
#### throws
```
/* 1. Throwing Function / Method
 * 아래의 throws 키워드는 함수 메서드 생성자 클로저가 에러를 던질 수 있다고 선언하는 것이다. */
func name(parameters) throws -> ReturnType {
    statements
}

/* Throwing Initializer */
init(parameters) throws {
    statements
}

/* Throwing Closure */
{ (parameters) throws -> ReturnType in
    statements
}
```

Throwing function 을 선언해보자.
```
enum DataParsingError: Error {
    case invalidType
    case invalidField
    case missingRequiredField(String)
}

func parsing(data: [String: Any]) throws {
    //parameter 로 전달된 딕셔너리에 name 키가 없다면 missing required field 에러를 전달하는 코드를 구현
    guard let _ = data["name"] else {
        throw DataParsingError.missingRequiredField("name")
        /* 앞에서 선언한 열거형 케이스를 리턴한다.
         * throw 문은 return 문과 마찬가지로 코드를 즉시 종료한다.
         * throw 문이 호출 되면 같은 블럭의 나머지 코드는 실행되지 않는다. */
    }
    
    guard let _ = data["age"] as? Int else {
        throw DataParsingError.invalidType
    }
    /* 파라미터로 전달된 딕셔너리에 name 키가 추가되어 있고,
     * age 키에 저장된 값을 Int 로 캐스팅 할 수 있다면 throw 문은 호출되지 않는다.
     * 그러나 throws 를 삭제할 수는 없다.
     * 거꾸로 이야기 해보면, throws 키워드가 있다고 해서 코드 블럭에서 항상 throw 문을 호출해야 하는 것은 아니다.
     * 코드가 에러 없이 정상적으로 호출 되면 throw 문은 호출 될 필요가 없다. 
     * throw 문은 에러가 발생된 경우에만 호출되어야 한다. */
    
    // Parsing ... (omitted)
}
```

### try Statements
함수를 호출해보자.
함수를 호출할 때는 try 표현식을 사용한다. 

expression 부분에는 주로 throwing function, throwing method, throwing Initializer, Throwing Closure가 사용된다.
```
try expression  // try. do catch 문과 주로 사용한다.
try? expression // optional 이 결합된 형태. optional try. 표현식에서 에러가 발생할 경우 nil을 리턴한다.
try! expression // optional 이 결합된 형태. forced try. 에러가 발생할 경우 런타임 에러가 발생한다. 가능하다면 사용하지 않는다. 
```

자동완성에서 오른쪽에 throws 가 표시되면 반드시 try 표현식으로 호출하도록 한다. 
여기에서는 optional try 로 호출해보도록 한다. 
```
func parsing(data: [String: Any]) throws {
    guard let _ = data["name"] else {
        throw DataParsingError.missingRequiredField("name") //여기에서 에러가 전달된다.
    }
    
    guard let _ = data["age"] as? Int else {
        throw DataParsingError.invalidType
    }
}

try? parsing(data: [:]) //파라미터로 빈 딕셔너리를 호출. nil을 리턴하고 호출문이 종료된다.
```

에러를 처리하는 방법
1. do-catch Statements
2. try Expression + Optional Binding
3. hand over


