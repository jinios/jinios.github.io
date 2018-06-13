---
layout: post
title: extension에서 Override
tags: swift extension override
categories: Swift
---

### @objc
> super클래스에서 extension으로 함수를 추가하고 sub클래스에서 override 하고싶을땐 `@objc`키워드를 붙여야한다.

### 발견
UIView extension에서 새로운 함수를 선언하고 다른 커스텀뷰(UIView타입)에서 해당 함수 내부를 좀 다르게 쓰고싶어서 `override`를 선언하니까, `Overriding non-@objc declarations from extensions is not supported`라는 에러 발견.


### 해결

그럼 `@objc` 붙이면 해결!

```swift
class MySuperClass {

}

extension MySuperClass {
    @objc func otherNumber() -> Int {
        return 1
    }
}

class MySubclass : MySuperClass {
    @objc override func otherNumber() -> Int {
        return 2
    }
}

var sup = MySuperClass()
var sub = MySubclass()
print(sup.otherNumber()) // 1
print(sub.otherNumber()) // 2

```

#### (고민)
- 처음에 이 방법을 쓰려고 했던건 커스텀 뷰 클래스에서도 비슷한 동작을 해야하지만, UIView클래스에 선언해놓은 것 처럼 완전히 같게 동작할 수는 없어서였음
- 비슷한 동작이고, 같은 클래스타입이니까 같은 이름으로 사용하고싶어서 override했던 것인데,
- 애초에 해당 타입에다가 필요한 함수를 extension으로 구현해놓고 그 함수를 또 `override`해서 변경하고 사용하는게 합리적인걸까?
