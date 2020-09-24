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


## do-catch Statements
do-catch Statements 의 문법은 아래와 같다. 
```
do { //do block은 필수 블럭이다
    try expression //에러가 발생할 수 있는 코드를 실행한다.
    statements
} catch pattern { //try 표현식에서 에러가 발생하면 do block에서 이어지는 코드는 실행되지 않고 catch 블럭이 실행된다. 
    statements
} catch pattern where condition { //where절을 추가하여 매칭시킬 에러패턴의 조건을 추가할 수 있다. 
    statements
}
```
do block에서 발생 가능한 모든 에러는 catch 블럭을 통해 모두 처리되어야 한다.
catch 블럭을 생략한 경우에는 에러가 다른 코드로 전파될 수 있도록 구형해야만 한다. 

```
enum DataParsingError: Error {
    case invalidType
    case invalidField
    case missingRequiredField(String)
}

func parsing(data: [String: Any]) throws {
    guard let _ = data["name"] else {
        throw DataParsingError.missingRequiredField("name")
    }
    
    guard let _ = data["age"] as? Int else {
        throw DataParsingError.invalidType
    }
    
    // Parsing ... (omitted)
}

do {
    try parsing(data: [:]) //parsing 데이터 함수는 throwing function 이기 때문에 throwing 표현식을 통해 호출하여야 한다. 
} catch DataParsingError.invalidType {
    print("invalid type error")
} catch { //error pattern 생략
    print("handle error")
} 
/* 위 코드를 실행하면 파싱 함수에서 첫번째 가드문의 else block이 실행된다. 
 * 그리고 그곳에서 던지는 에러가 do-catch block으로 전달된다. 
 * 첫번째 block과는 매칭되지 않고 두번째 block에 매칭된다. (handle error 가 출력됨)
 */
```

catch block을 작성할 때에는 까다로운 패턴부터 작성해야 한다. 
catch block간의 순서룰 바꾸어보자.
```
do {
    try parsing(data: ["name":""])  //함수를 호출할 때 전달하는 값 수정
} catch { 
    print("handle error")
} catch DataParsingError.invalidType {
    print("invalid type error")
} 
```
위 코드처럼 작성한다고 해도 에러는 발생하지 않는다.
그러나 어떤 에러가 발생하더라도 항상 첫번째 catch block의 handle error 가 출력될 것이다. 
위 코드에서 invalid type error가 전달되기 때문에 두번째 블록이 실행될 것 같지만,
항상 첫번째 블록과 매칭되기 때문에 두번째 블록은 실행되지 않는다. 
그래서 패턴이 생략된 캐치 블록은 항상 마지막에 작성해야 한다. 
그리고 패턴이 여러개 있는 경우에는, 가장 까다로운 패턴부터 작성하도록 한다. 

다시 아래와 같이 바꾸어보자.
```
do {
    try parsing(data: ["name":""]) 
} catch DataParsingError.invalidType {
    print("invalid type error")
} catch { 
    print("handle error")
} 
```
이번에는 invalid type error와 매칭되고 invalid type error 가 출력된다.

마지막 캐치 블럭을 삭제해보자.
현재는 do-catch block이 global scope에서 작성되었다. 
이 경우에는 모든 에러를 처리하지 않아도 컴파일 에러가 발생하지 않는다.
그러나 실제 프로젝트에서는 이렇게 global scope에서 작성하는 경우는 없다.
```
do {
    try parsing(data: ["name":""]) 
} catch DataParsingError.invalidType {
    print("invalid type error")
} 
```

코드를 함수 내부로 이동시켜보자. 
```
func handleError() {
  do {
    try parsing(data: ["name":""]) 
  } catch DataParsingError.invalidType {
    print("invalid type error")
  } 
}
/* compile error! */
```
do-catch 문은 반드시 do 블럭에서 발생할 수 있는 모든 에러를 catch block에서 처리해야 한다.
에러를 해결하는 방법은 두 가지이다.
이전 코드처럼 발생할 수 있는 모든 에러를 처리할 수 있는 catch block을 구현하거나,
handle error 함수가 나머지 에러를 다른 코드로 던지도록 선언한다. 

```
func handleError() throws { //throws 키워드를 추가하여 invalid type을 제외한 나머지 타입의 에러를, handleError를 호출한 코드로 전달한다.
  do {
    try parsing(data: ["name":""]) 
  } catch DataParsingError.invalidType {
    print("invalid type error")
  } 
}
```

아래와 같이 catch block을 모두 생략하고 에러를 무조건 던지도록 구현할 수도 있다. 
```
func handleError() throws {
  do {
    try parsing(data: ["name":""]) 
  }
}
```

위와 같은 경우에는 do-catch 문을 쓰지 않고 try 표현식을 바로 호출해도 문제가 없다. 
```
func handleError() throws {
  try parsing(data: ["name":""]) 
}
```

### 패턴이 없는 catch block
패턴이 없는 catch block은 대부분 아래와 같이 구현한다. 
```
func handleError() throws {
  do {
    try parsing(data: ["name":""]) 
  } catch {
    /* 어떤 에러가 발생했는지 판단 할 수단이 필요하다.
     * 패턴이 없는 catch block에는 error라는 특별한 local 상수가 제공된다. 
     * error 상수의 형식이 실제 에러 형식이 아니라 에러 프로토콜이기 때문에
     * type casting 이 필요하다. 
     */
    if let error = error as? DataParsingError {
        switch error {
            case .invalidType:
                print("invalid type")
            default:
                print("handle error")
        }
    }
  }
}
```

## Optional Try
에러를 던지는 함수나 생성자를 호출할 때는 try 표현식을 사용해야 한다. 
do block이 아닌 다른 곳에서 호출할 때는 optional try, forced try 를 사용한다. 
optional try는 표현식에서 에러가 전달된 경우 nil을 return한다.
반면 forced try는 실행을 중지한다. 다시 말해서 런타임 에러가 발생한다. 
```
try? expression //Optional try
try! expression //Forced try
```

```
enum DataParsingError: Error {
    case invalidType
    case invalidField
    case missingRequiredField(String)
}

func parsing(data: [String: Any]) throws {
    guard let _ = data["name"] else {
        throw DataParsingError.missingRequiredField("name")
    }
    
    guard let _ = data["age"] as? Int else {
        throw DataParsingError.invalidType
    }
    
    // Parsing ... (omitted)
}

if let _ = try? parsing(data: [:]) {
    print("success")
} else {
    print("fail") //binding 에 실패한 경우. 빈 딕셔너리를 전달한 경우 이 문자열이 출력된다.
}

/* 위 코드를 do catch 문으로 구현해보자 */
do {
    try parsing(data: [:])
    print("success")
} catch {
    print("fail")
} 
/* if block 은 do block 으로,
 * else block은 pattern 이 없는 catch block 으로 구현했다. */

/* optional try를 사용할 때 반드시 optional binding 을 사용해야 하는 것은 아니다.
그냥 함수만 호출하고 결과는 신경 쓸 필요가 없다면, optional try만 작성한다. */
try? parsing(data: [:])

try! parsing(data: ["name": "steve", "age": 33]) //parameter로 올바른 dictionary 전달. 아무 문제가 없다.
try! parsing(data: [:]) //runtime error가 발생한다. 실제 디바이스에서 실행한다면 crash 가 발생한다. 

/* forced try는 표현식에서 발생한 에러를 다른 곳으로 전달할 수 없다. 그리고 do-catch 를 통해 에러를 처리하는 것도 불가능하다.
   에러가 발생하는 즉시 프로그램이 강제로 종료되기 때문이다. 따라서 forced try는 사용하지 않는 것이 좋다. 
   에러가 발생하지 않는 것이 확실한 경우에만 제한적으로 사용해야 한다. */
```

### defer Statements
defer문은 코드의 실행을 scope가 종료되는 시점으로 연기시킨다.
주로 코드에서 사용했던 자원을 정리할 때 사용한다.

```
defer {
    statements
}
```
defer문을 호출하면 블록에 포함된 코드가 바로 실행되지는 않는다.
대신 defer 문이 호출된 scope의 실행이 종료될 때 까지 연기된다.

아래 함수는 parameter 로 전달된 경로에서 파일을 loading 을 한 후, 
필요한 작업을 하고 파일을 닫는다.
파일 처리에서는 파일을 여는 것 보다 닫는 것이 중요하다.
코드가 길어짐에 따라 종종 파일을 닫지 않았는데 함수가 종료되는 경우가 있다. 
```
func processFile(path: String) {
    let file = FileHandle(forReadingAtPath: path)
    
    //process

    
    if path.hasSuffix(".jpg") {
        return
    }
    
    file?.closeFile() 
}
```

defer 문을 사용하면 문제를 해결할 수 있다.
defer문의 실행은 함수가 종료될 때 까지 연기된다.
런타임 오류가 발생해서 프로그램이 비정상적으로 종료되는 경우를 제외하고 항상 함수가 종료되는 시점에 실행된다.
그래서 함수가 복잡하더라도 항상 closeFile method를 실행한다.
아래 코드에서는 실행 순서를 파악하기 위해서 간단한 로그를 추가했다. 
```
func processFile(path: String) {
    print("1")
    let file = FileHandle(forReadingAtPath: path)
    
    //process
    
    defer {
        print("2")
        file?.closeFile()
    }
    
    if path.hasSuffix(".jpg") {
        print("3")
        return
    }

    print("4")
}

processFile(path: "file.swift")
```

호출 순서는 아래와 같다.
먼저 FileHandle 이 생성된 다음, 이어서 defer문이 호출된다.
그러나 defer 블록에 있는 코드는 바로 실행되지 않고 scope종료시점으로 연기된다.
그래서 if문이 실행된다. parameter로 swift 확장자가 전달,
condition이 false로 평가되므로 if문 다음에 있는 코드가 실행된다.
여기에서 print 함수가 호출된 후 함수 실행이 종료된다.
이 시점에서 defer 블록이 실행된다.

1, 4, 2의 순서로 출력된다.

확장자를 jpg로 바꾸고 다시 실행한다. 
```
func processFile(path: String) {
    print("1")
    let file = FileHandle(forReadingAtPath: path)
    
    //process
    
    defer {
        print("2")
        file?.closeFile()
    }
    
    if path.hasSuffix(".jpg") {
        print("3")
        return
    }

    print("4")
}

processFile(path: "file.jpg")
```

이번에는 1, 3, 2의 순으로 출력된다. 

if문 뒤에 defer 문을 출력해보자. 
```
func processFile(path: String) {
    print("1")
    let file = FileHandle(forReadingAtPath: path)
    
    //process
    
    defer {
        print("2")
        file?.closeFile()
    }
    
    if path.hasSuffix(".jpg") {
        print("3")
        return
    }
    
    defer {
        print("5")
    }

    print("4")
}

processFile(path: "file.jpg")
```

위 코드에서 5는 출력되지 않고 1, 3, 2 순으로 출력된다. 
지금은 jpg 확장자가 전달되기 때문에 방금 추가한 defer 문은 호출되지 않는다.
defer 문을 구현했다고 해서 항상 defer 블록에 포함된 코드가 실행되는 것은 아니다.
반드시 defer 문이 호출되어야 종료시점에 실행할 코드가 예약된다.
이렇게 조건에 따라 defer문이 호출되지 않는 문제를 막기 위해서 주로 scope 시작 부분에 defer 문을 구현한다.

아래와 같은 함수를 호출하면 어떤 순서대로 디버그 문이 출력될까?
```
func testDefer() {
    defer {
        print(1)
    }
    
    defer {
        print(2)
    }
    
    defer {
        print(3)
    }
}

testDefer()
```

정답은 3, 2, 1 순이다. 
defer문은 호출된 순서대로 코드를 예약한다. 
여기서는 1을 출력하는 첫번째 블록이 가장 먼저 예약된다.
예약된 블록이 실행될 때는 가장 마지막에 예약된 블록이 먼저 실행된다.
defer문이 호출된 순서와 반대이다. 
이 순서를 모른다면 논리적 오류가 발생할 위험이 있으므로 순서를 인지한다. 
또한 이러한 오류를 막기 위하여 하나의 defer문만 사용하도록 한다. 
