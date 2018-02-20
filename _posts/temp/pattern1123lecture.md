### 11/23 피드백강의
### 패턴
> enum의 연관값 예제코드를 질문했더니 패턴과 관련하여 강의를 해주셨다. 스위프트 패턴 관련 강의정리!

#### 값 바인딩
enum에 연관값이 있을때 연관값이 무엇인지도 알아야하고 그것을 사용하기 위해서는 한번 더 비교해서 값을 꺼낸다는 개념! 즉 새 변수에 해당 값을 바인딩(묶기)위함임.
~~~swift
let point = (3,2)
switch point {
case let (x,y):
 print("hello at \(x),\(y)")
}
switch point {
case (let i, _):
 print("hello loop \(i) only")
}
switch point {
case (_, var j):
 j += 10
 print("hello loop \(j) only")
~~~
- point의 (3,2)는 각 case마다 `let`이나 `var`로 바인딩되고 있다. (자리 수로 구분함, 예를들어 두번째 case의 i는 튜플의 앞자리니까 point.0임)
- 세번째 case의 j가 var로 선언된 이유는 : point.1값을 꺼내서 변수 j에 바인딩하고, 그 값에 10을 더하는 동작이 있으니 변수 `var`로 선언되었다.


#### 열거형 케이스
~~~Swift
let aString = "ABC"
if case "ABC" = aString { \// }
enum Dish {
 case pasta(taste: String)
 case chicken(withSauce: Bool)
 case airRice
} //연관값이 있는 pasta와 chicken

var dishes = [Dish]()
var myDinner : Dish = .pasta(taste: "cream") //"cream"연관값이 있는 pasta
dishes.append(myDinner)
if case .pasta(let taste) = myDinner { print(taste) }
//.pasta의 taste(연관값)가 "cream"이면 let taste(새 변수)에 할당하고 taste(변수)를 프린트
//여기서 taste(연관값)과 taste(변수)는 다름

myDinner = .chicken(withSauce: true)
dishes.append(myDinner)
while case .chicken(let sauced) = myDinner {
 break
}

myDinner = .airRice
dishes.append(myDinner)
if case .airRice = myDinner { //동작 }

for dish in dishes {
 switch dish {
 case let .pasta(taste): print(taste)
 case let .chicken(sauced): print(sauced ? "양념" : "후라이드")
 case .airRice: print("공기밥")
 }
}
~~~



#### 옵셔널
~~~Swift
var someOptional : Int? = 42

if case .some(let x) = someOptional {print(x)} //옵셔널은 타입 자체가 enum이고 .some은 연관값이 있는 케이스이다. 옵셔널enum에서 .some케이스에 연관값을 옵셔널바인딩해서 값할당
if case let x? = someOptional {print(x)} //위의 코드랑 동일한 작용임. someOptional을 언박싱하고 값이 들어있으면 x에 42가 할당된다.

~~~


### 타입변환
~~~swift
let someValue: Any = 100
swicth someValue {
 case is String: print("문자열이었어!")
 case let value as Int: print(value+1)
 default: print("Int도 아니도 String도 아니네!")
~~~

• (pattern) as (type)
변환해보고 값이 있으면 (타입변환에서 as): `value`를 Int로 변환해보고 값이 있으면 `value:Int`로 쓴다


override - (상속의 관계일때)재정의 하위클래스가 슈퍼클래스에있는 메소드를 "재정의"하는 뜻
overload - operator는 오버로드 - 덮어씌움


#### where절
바인드를 하고 그 값을 비교를 하고싶을때 case를 넣어서 바인드를 하되 조건이 무엇이냐 할떄
