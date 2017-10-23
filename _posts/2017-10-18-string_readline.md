---
layout: post
title: String에 문자열 더할때, 스위프트에서 사용자 입력값 받을때 readline()
tags: swift iOS string
categories: Swift
---



### String을 더할 때

String을 더할땐 + 연산자와 append 함수 방식이 있음

1) 문자열 덧붙이기

 : + 연산자와 append 함수가 있습니다.

\+ 연산자 : 문자열과 문자를 혼용할 수 없다.

```swift
var str = "Hello"
str = str + " Swift" // 성공
print(str)
 
str = str + "!" // 성공
print(str)
 
let char : Character = "@" // 실패
str = str + char
print(str)
```

append 함수 : 문자열과 문자를 혼용 가능

```swift
var str = "Hello"
str.append(" Swift") // 성공
print(str)
 
str.append("!") // 성공
print(str)
 
let char : Character = "@" // 성공
str.append(char)
print(str)
```





### 스위프트에서 입력값 받기

```swift
func userInput() -> String {
    let inputValue = readLine()
    print("사용자 입력값: " + inputValue!)
    return inputValue!;
}
```

[참고블로그](http://zeddios.tistory.com/68)



(+추가..)

readline()함수를 먼저 연습해보고싶어서 플레이그라운드에서 실행시켰는데, "Expressions are not allowed at the top level' if the module is not main.swift" 라는 에러가 났다. 이유는 아래와 같음..

- 꼭 메인에서만 실행되야함… 
- 플레이그라운드에서는 실행이 안된다 (플레이그라운드는 샌드박스이기때문에..)
- 간단히 readline()을 실습해보려고 같은 프로젝트 내에 swift파일을 만들었는데 메인이 아니라 실행이안됨 ㅠㅠ
- 자바에서 TDD를 위해 쓰였지만 메인 없이도 테스트해볼 수 있었던 제이유닛같은게 스위프트에도 있을까..?