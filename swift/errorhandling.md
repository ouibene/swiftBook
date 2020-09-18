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

