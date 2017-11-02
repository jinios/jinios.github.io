---
layout: post
title: 간접참조, 형변환, String(문자열)자르기, index()함수
tags: swift iOS 
categories: Swift
---

#### 간접참조 Indirection

값 자체보다 컨테이너, 연결, 별명등을 사용해서 우회하도록 참조하는 방식

메소드나 함수를 호출하는 것 (함수를 만들어놓고 호스트코드가 필요한 함수를 호출하는 과정): sub-routine 

호스트코드 : 함수를 호출하는 코드 

let (상수) var (변수)와 같은 변수선언 또 한 간접참조

스위프트의 데이터타입 : 객체처럼 구상되어있어서 var a : Int = 99

이면 a._ _ _ 했을때 사용가능한 함수들이 나옴 (자바에서 클래스의 메소드 호출하는 방법)

모바일은 메모리가 항상 부족하기때문에 데이터타입의 메모리문제로, 데이터타입을 선택할때 더 신중해야함



#### 형변환

Int(3.14) - 3.14가 Int타입으로 바뀜

Double(10) - 10이 Double타입으로 바뀜 



#### String 자르기

```swift
var str = "Hello!"

let test = str.substring(from: str.index(str.endIndex, offsetBy : -2))

let test2 = str.substring(to: str.index(str.endIndex, offsetBy : -2))

```



#### index() 함수와 index / 공백으로 문자열 나누기

정의 : index(of: "a") 하면 a 문자가 인덱스 자체가 되어서 문자열 인덱스로 리턴하는 의미 

```swift
public func index(of element: Character) -> String.Index?

//input 문장에 공백을 인덱스로 하고싶을때
var indexSet = input.index(of: " ") ?? input.endIndex
//이제 index가 " "으로, 그 자체가 인덱스로 정해진 상태

//indexSet기준으로 첫 단어만 표시
var firstWord = input[..<indexSet]

//indexSet 기준으로 마지막 단어만 표시하고싶을때
var lastWord = input[input.index(after: indexSet) ..< input.index(before: input.endIndex)] 

/*
input.index(after: indexSet) - input의 index(indexSet)바로 뒤에 있는 문자를 인덱싱
input.index(before: input.endIndex) - input의 맨 끝글자의 바로 앞에있는 문자를 인덱싱
*/
```





#### 