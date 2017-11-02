---
layout: post
title: 스위프트의 "함수" 
tags: 함수 parameter swift function
categories: Swift
---

### 함수

#### Parameter

외부 파라미터 / 내부 파라미터 

함수의 가독성을 위해서 함수를 호출할 때 사용하는 파라미터의 이름과 함수 안에 구현된 파라미터의 이름이 다르다.

아래는 외부파라미터가 가독성을 높일 수 있다는 것을 알려주는 예제, 구어처럼 전치사 to를 외부 파라미터로 사용했다.

~~~swift
func 함수명 (외부파라미터 내부파라미터: 타입){}
~~~

~~~swift
func printGreeting(to name: String){
  print("Hello \(name), welcome!")
}

// 함수호출
printGreeting(to: "Aliens")
~~~



#### 가변인자

보통 함수는 미리 정의된 형식과 개수에 맞는 인자값만 처리하지만, 스위프트에서는 함수가 가변적인 개수의 인자값을 입력받을 수 있도록 지원한다. 

아래와 같이 매개변수 타입에 "…"을 적어주면 가변인자로 인식되어 **개수를 제한하지 않고 인자값을 입력받고**, 입력받은 인자값은 **배열에 저장한다!**

~~~swift
func 함수이름(매개변수명 : 매개변수 타입 ...)
~~~

~~~swift
func average(score: Int...) -> Double {
  var total = 0
  for r in score {
    total += r
  }
  return (Double(total)/Double(score.count))
}

average(score: 10,20,30,40)
~~~

~~~swift
func printGreeting(to all: String...){
  for name in all{
    print("Hello \(name), welcome!")
  }
}
printGreeting(to: "Woody","Buzz","Aliens","Mr.Potato Head"

/*
결과:
Hello Woody, welcome!
Hello Buzz, welcome!
Hello Aliens, welcome!
Hello Mr.Potato Head, welcome!              
*/              
              
~~~



#### 기본값을 갖는 매개변수

함수의 정의 시 매개변수에 기본값을 지정하면 해당 함수 호출 시 인자값을 생략할 수 있다. 생략하면 기본값이 매개변수로 전달되어 함수가 실행되고, 함수 호출 시 기본값이 정해진 파라미터에 새로 값을 주게되면 새로 주어진 값이 파라미터로 전달되어 함수가 실행된다. (따라서 매개변수에 기본값을 주면 함수는 두 가지 형식 *- 기본값으로 동작 / 새로 넘겨주는 값으로 동작 -* 으로 모두 생성된다고 할 수 있다.

~~~swift
func 함수이름(매개변수: 매개변수 타입 = 기본값){
  실행할 내용
}
~~~

~~~swift
func divideNum(numerator: Double, denominator: Double, withPunctuation punctuation: String = "^_^"){
    print("\(numerator)/\(denominator) equals \(numerator/denominator)\(punctuation)")
}

divideNum(numerator: 9.0, denominator: 3.0)
divideNum(numerator: 9.0, denominator: 3.0, withPunctuation: "!")

/*
결과 : punctuation부분이 달라짐
9.0/3.0 equals 3.0^_^
9.0/3.0 equals 3.0!
*/

~~~



#### 인아웃 파라미터

~~~swift
var error = "The request failed:"
func appendErrorCode(_ code: Int, toErrorString errorString: inout String){
    if code == 400 {
        errorString += "bad request."
    }
}

appendErrorCode(400, toErrorString: &error)

//The request failed: bad request.
~~~



#### 복수 리턴값 - 튜플 사용하기 예제

~~~swift
func sortedEvenOddNumbers(_ numbers: [Int]) -> (evens: [Int], odds: [Int]){
    var evens = [Int]()
    var odds = [Int]()
    for number in numbers {
        if number % 2 == 0{
            evens.append(number)
        } else {
            odds.append(number)
        }
    }
    return (evens, odds)
}

let aBunchOfNumbers = [10,1,3,55,66,78,93,103]
let theSortedNumbers = sortedEvenOddNumbers(aBunchOfNumbers)
print ("Even numbers: \(theSortedNumbers.evens)")
print ("Odd numbers: \(theSortedNumbers.odds)")
~~~

