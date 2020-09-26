# Availability Condition
API 가용성을 확인하는 방법이다. 

버전에 따라 속성에서 에러가 발생할 수도 있다.
사용 가능한 버전을 확인하기 위해서는 속성 위에서 option key를 누르면서 클릭하면 나타나는 reference 를 참조한다. 
fix it으로 에러가 나는 부분을 고칠 수도 있다. 

Availability Condition은 아래와 같은 문법을 가지고 있다. 

```
if #available(OS Version, OS Version, *) { //runtime 에 버전을 확인한다. 
 /* API를 사용할 수 있는 OS이름과 버전을 사용한다. 보통 하나의 OS를 사용하지만 필요에 따라 두개 이상을 사용하기도 한다. 
  * OS이름으로 사용할 수 있는 값은 Apple 에서 제공하는 OS 이다. 
  * iOS, macOS 의 경우 extension 을지정하는 것도 가능하다. 그리고 OS 뒤에 오는 Version 은 숫자로 나타내고, 최소 버전을 의미한다.
  * asterisk 는 앞에서 선언하지 않은 모든 플랫폼을 나타내며, 생략할 수 없다. 
  */
} else {
 /* 지원하지 않는 기능에 대한 안내 메시지를 나타내거나, 이전 버전에 적합한 별도의 UI 를 구현하기도 한다. */
}

while #available(OS Version, *) {

}

guard #available(OS Version, *) else {
  return
}
```

### 여러 개의 조건을 추가하는 경우
logical and 연산을 사용하면 에러가 발생한다.
아래와 같이 작성하거나,
```
if #available(iOS 11.0, *), #available(mac OS 11.0, *) {

} else {

}
```

괄호 안에서 버전을 추가하도록 한다. 
```
if #available(iOS 11.0, macOS 10.3, *) {

} else {

}
```
