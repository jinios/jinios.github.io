---
layout: post
title: 조건문 switch-case, fallthrough
tags: swift ios
categories: Swift
---



### switch-case

```swift
switch <비교대상> {
    case <비교패턴1> :
  	<비교 패턴1이 일치했을때 실행할 구문>
  	case <비교패턴2>, <비교패턴3> : //(ex. case 2, 3: )
  	<비교 패턴 2가 일치했을때 실행할 구문>
  	default :
  	<어느 비교패턴과도 일치하지 않았을때 실행할 구문>
}
```

- 일치하는 비교패턴이 있을경우 해당 블록을 실행하고 전체 분기문 종료
- 일치하는 비교패턴이 또 있더라도 일치하는 것 하나만 실행하고 종료함



#### fallthrough

```swift
let sampleChar: Character = "a"

switch sampleChar {
    case "a" :
  		fallthrough
  	case "A" :
  		print("글자는 A입니다.")
  	default : 
  		print("일치하는 글자가 없습니다.")
}
```

- case문이 비어있으면 안됨
- 명시적으로 fallthrough구문을 사용하면 해당 case문(case1)은 비교패턴이 일치한 경우 그 다음 case(case2)문으로 흐름을 전달 
- 실행흐름을 전달받은 case2구문은 비교패턴의 일치여부와 상관없이 작성된 구문을 실행한후 switch문을 종료
- default : case문에서 일치하는 케이스를 찾지 못했을 경우를 대비하여 default가 반드시 필요하고, **모든 case문에 모든 패턴을 매칭시킬 수 있는 경우에 한해** default문을 생략할 수 있다. 

```swift
var value = (2,3)

switch value {
    case let (x,3) :
  		print("튜플의 두 번째 값이 3일때 첫번째 값은 \(x)입니다")
    case let (x,3) :
  		print(abc)
    case let (x,3) :
  		print(abc)
}
```

- 출력함수 내에서 변수를 받아서 출력할때 \ (변수이름)



### 