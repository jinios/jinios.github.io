---
layout: post
title: 프로퍼티-연산형 프로퍼티-getter, setter
tags: getter setter swift 프로퍼티
categories: Swift
---



### 프로퍼티의 사용

#### 연산형 프로퍼티

연산형 프로퍼티는 지금까지 다룬 프로퍼티와는 다르게 값을 저장하지 않고, getter와 setter라는 것을 제공한다. 이 때문에 계산형 프로퍼티의 값은 변경될 수 있다.

- getter : 프로퍼티의 값을 가져옴
- setter : 값을 설정함 (단, setter는 필수 요소가 아니다.)



먼저 전체 예시코드

~~~swift
//main.swift

struct Point {
    var x = 0.0, y = 0.0
}

struct Size {
    var width = 0.0, height = 0.0
}

struct Rect {
    var origin = Point()
    var size = Size()
    var center : Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
            //center는 Point타입이고 get안의 로직에 따라 연산된 값의 x,y
        } set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        } //newCenter는 setter의 매개변수 같은 것이다.
    }
}

var square = Rect(origin: Point(x: 0.0, y: 0.0), size: Size(width:10.0, height: 10.0))
let initialSquareCenter = square.center 
square.center = Point(x: 15.0, y: 15.0) 

print ("initialSquareCenter is now at (\(initialSquareCenter.x), \(initialSquareCenter.y))")
print("square.origin is now at (\(square.origin.x),\(square.origin.y))")


/*
결과 : 
initialSquareCenter is now at (5.0, 5.0)
square.origin is now at (10.0,10.0)
*/


~~~



#### getter

~~~swift
var center : Point {
get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
            //center는 Point타입이고 get안의 로직에 따라 연산된 값의 x,y
        }
   ...
   
var square = Rect(origin: Point(x: 0.0, y: 0.0), size: Size(width:10.0, height: 10.0))
let initialSquareCenter = square.center
~~~

- ` var center : Point {}`처럼 계산형 프로퍼티를 정의할때는 타입을 꼭 명시해야 함. 여기서 `center`라는 계산형프로퍼티는 `Point`타입이라는것을 명시.

- getter : Point타입의 value를 리턴하고, getter만으로 값이 복사되는 것은 아님! (연산만 하는 것) 

- square 의 이름으로 Rect타입 구조체의 인스턴스가 만들어졌다. 이때 초기화된 값은 origin (0,0), size(10,10) 

- square라는 인스턴스의 count라는 프로퍼티는 연산형 프로퍼티이다. `대입연산자 =` 옆에 연산형프로퍼티를 호출하는게 있으면 getter를 부르는 것인데, `let initialSquareCenter = square.center` 는 center의 get안의 로직에 따라 연산된 값이 initialSquareCenter에 값을 복사한다는 뜻임

  ​



#### setter

~~~swift
var center : Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        } set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.width / 2)
        }
        //setter에서, set(newCenter) = func setCenter(newCenter: Point) 와 같은뜻. newCenter는 set의 매개변수
    }
}
...

var square = Rect(origin: Point(x: 0.0, y: 0.0), size: Size(width:10.0, height: 10.0))

...

square.center = Point(x: 15.0, y: 15.0)


~~~

- `set(newCenter)` 는 `set(newCenter) = func setCenter(newCenter: Point)`처럼 작동함. (newCenter는 setter의 매개변수처럼 작동)
- ` square.center = Point(x: 15.0, y: 15.0)` 처럼 왼쪽에 square.center가 있으면 set의 로직에 따라 newCenter에 (x: 15.0, y: 15.0)에 매개변수로 들어가서 계산된 값이 origin.x / y에 복사 