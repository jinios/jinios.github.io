---
layout: post
title: Call by value / reference
tags: Swift value reference
categories: Swift
---



#### call by value

~~~ swift
let origin = CGPoint(x:0, y:0)
var other = origin //인스턴스를 복사하면 변수에 값이 그대로 복사됨 (call by value) 
other.x += 10

/*
좌표값은 아래와같음. 복사된 값이기때문에 other의 값을 바꿔도 origin의 값이 바뀌지 않는다.
origin > (0,0)
other > (10,0)
*/
~~~

위에서 origin의 좌표값 (x:0, y:0)은 변수가 바뀌었을때 값이 그대로 복사되는 것들이다. - value semantics

함수를 호출할때도 value semantics들은 보통 값이 복사가 됨



#### Call by Reference Types

클래스는 항상 간접참조를 한다 - 값을 직접 참조하지 않음. 

~~~swift
var myMilk = ChocoMilk() //myMilk는 ChocoMilk를 참조함
mymilk.amount = 300
var yourMilk = myMilk //call by reference
yourMilk.amount = 100

print(myMilk.amout) // amount = 100
~~~



#### Value Type Reference

inout은 - value타입도 그 함수 안에서만 reference로쓸수있도록 강제하기도 함 (C와의 호환성을 위해서 씀)

~~~swift
let origin = CGPoint(x:0, y:0)
var other = origin 
other.x += 10
var another = origin
another.y += 5

func swapPoint(pointA : inout CGPoint, pointB : inout CGPoint) {
  let temp = pointA
  pointA = pointB
  pointB = temp
}

swapPoint(pointA: &other, pointB: &another)
print(other, another)
//(0,5)(10,0)
~~~





#### Memory Model

프로세스 메모리모델 (운영체제가 갖고있는)

text영역에 코드 들을 순서대로 올려놓고 한줄씩 실행

BSS와 GVAR부분에 전역변수 선언

stack, heap

stack - 함수호출, 내부변수(상수), value type 인스턴스

함수를 호출할때마다 리턴할 주소, 아규먼트, 매개변수로 넘어가는 값들을 복사

call stack - 스택에 쌓이면서 함수가 실행

함수가 빠져나가면 스택의 맨 위에 쌓여있는건 없어지고 그 다음에잇던 함수가 실행됨

heap - 클래스의 인스턴스 는 heap에 쌓임 / 그 인스턴스의 변수는 stack에 생김 

chocoMilk는 heap / milk는 stack - milk는 chocoMilk를 향해 화살표가 생김



#### identity / equality

![identityEquality](/temp/identityEquality.png)

myPen과 yourPen은 같은 클래스에서 만들어졌지만 다른 인스턴스

myPen & whichPen : identity - 둘다 같은 객체인 myPen을 참조 - 주소값까지 같음

myPen & whichPen  VS  yourPen : equality - 값만 같음



자주 생성되고 없어지는 객체인지 아니면 한번 만들고 참조해서 쓰는게 좋은 객체인지 판단해서 class를쓸지 struct를쓸지 판단해야함

