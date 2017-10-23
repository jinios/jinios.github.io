---
layout: post
title: 튜플과 타입알리어스(Tuple,Typealias)
tags: 튜플 ios swift 타입알리어스
categories: Swift
---

### 튜플

한 함수에서 여러 개의 값을 리턴해야한다면, 이 값을 담을 수 있는 집단 자료형이 필요하다. 이러한 집단 자료형은 대표적으로 딕셔너리, 배열, 튜플, 구조체, 클래스가 있음

- 튜플에는 여러 종류의 자료형이 다양하게 섞여 정의될 수 있음

```swift
func getUserInfo() -> (Int, String, Character) {
  let height = 170
  let name = "알린"
  let gender : Character = "F"
  //데이터 타입이 String으로 추론될 수 있으니까 타입 어노테이션 선언
  return (height, name, gender)
}
```

- 함수가 반환하는 튜플을 대입받은 변수나 상수는 튜플의 **인덱스**를 이용해서 튜플의 내부 요소를 사용할 수 있다. 

```swift
var uInfo = getUserInfo()
uInfo.0 // 170
uInfo.1 // "알린"
uInfo.2 // "F"
```

- 튜플 요소 각각을 변수로 직접 받을 수 있음

```swift
var (a,b,c) = getUserInfo()
a // 170
b // "알린"
c // "F"
```

- 일부 항목이 필요하지 않다면 언더바를 사용해서 변수 할당 없이 건너뛸 수 있음

```swift
var (height, _, name) = getUserInfo()
```

- 각 튜플의 아이템을 함수를 반환할때 미리 변수에 할당해 둘 수 있음.
  - 이렇게 하면 함수를 실행할 때 결과값을 바인딩하지 않아도 됨

```swift
func getUserInfo() -> (h: Int, n: String, g: Character) {
  let height = 170
  let name = "알린"
  let gender : Character = "F"
  
  return (height, name, gender)
}

// 결과값 변수 대입부분
var result = getUserInfo()

result.h // 170
result.n // "알린"
result.g // "F"
```



#### typealias 타입알리어스

- 이름이 길거나 사용하기 복잡한 타입 표현을 새로운 타입명으로 정의해주는 문법
- 타입알리어스를 사용하면 길고 복잡한 형태의 타입 표현도 짧게 줄일 수 있음
- 특정 튜플타입이 여러 곳에서 사용될 경우에는 타입 알리어스를 통해 **새로운 축약형 타입을 정의**하는 것이 좋음.

```swift
typealias <새로운 타입 이름> = <타입 표현>
```

- 타입 알리어스 정의 예시와 사용하는 방법

```swift
typealias userInfoResult = (Int, String, Character)
//타입알리어스 사용 (userInfoResult라는 타입 이름을 표현한 튜플타입과 같은 것으로 간주함)

func getUserInfo() -> userInfoResult {
  let height = 170
  let name = "알린"
  let gender : Character = "F"
  
  return (height, name, gender)
}

//사용부에는 아무 변화가 없다.
let info = getUserInfo()
info.0
info.1
info.2
```

> **사용부분에는 아무 변화가 없는데 사용하는 이유?**
>
> 타입표현을 새로운 타입명으로 정의해주는 문법. 주로 중복되거나 사용하기 복잡한 타입 표현을 재정립하는 용도로 사용된다.
>
> 보통 내부에 선언하는 Nested 타입을 줄여서 새로운 타입이름으로 줄여서 표현하는 정도. 튜플 같은 형태에 이름을 붙여주는 용도.

- 타입알리어스 를 사용하여 축약 표현 변수가 바인딩 된 튜플을 정의할 수도 있음

```swift
typealias userInfoResult = (h: Int, n: String, g: Character)

func getUserInfo() -> userInfoResult {
  let height = 170
  let name = "알린"
  let gender : Character = "F"
  
  return (height, name, gender)
}

let info = getUserInfo()
info.h
info.n
info.g
```

