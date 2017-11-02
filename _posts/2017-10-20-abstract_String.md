---
layout: post
title: 스위프트의 추상화 - String
tags: ios swift string abstract
categories: Swift
---

### 스위프트의 추상화 - String

String.Substring / String.index을 쓰면서 특이한 점을 발견했다.

스트링에서 잘라도, 어떤 한 스트링 중에서 지정한 인덱스여도 그 타입은 스트링이 아니고 substring, string.index 타입이다! (그래서 스트링과 다른 타입으로 인식해서 스트링으로 가정하고 코딩하면 컴파일러는 에러를 내뱉는다.)

스위프트가 이러한 방식을 차용한 이유는 메모리때문이라고 한다. **메모리를 적게 사용하기위해 객체의 속성도 값 대신 다른 객체로 추상화한다.** 



예를들어, 하나의 String이 있다.

let sayHello = "Hello Aliens"

여기서 "Hello"만 자르게 코딩하면, "Hello"는 또 하나의 스트링으로 선언되는게 아니라 sayHello의 substring타입이 되어, **sayHello의 전체 중 "Hello"부분만 바라보게 되는 방식이다.** (따라서, 만약 sayHello가 유효하지 않은 값이 되면 해당 스트링의 substring또한 유효하지 않게 된다.)



아래는 JK님이 추상화 관련해서 공유해주신 글.

[참고 글-함수형 프로그래밍이란?](https://medium.com/@lazysoul/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%B4%EB%9E%80-d881230f2a5e)

[참고 글 - 우리는 함수형 프로그래밍을 배워야 할까?](https://slipp.net/questions/170)

### 
