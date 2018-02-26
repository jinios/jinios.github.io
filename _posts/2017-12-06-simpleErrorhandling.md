---
layout: post
title: 에러핸들링 throw ~ do-try-catch
tags: error throw do-try-catch errorhandling
categories: Swift
---

### throw
에러가 발생할 수 있는(잘못될 수 있는) 함수에는 예외없이 `throw`붙어야한다. 함수에 `throw`를 붙이면 그 함수의 리턴타입은 "원래 리턴하던 타입이나 error프로콜의 인스턴스"로 변경된다.

### do-try-catch
- `do` : 정상적으로 코드를 작성하면 된다. `throw`가 지정된 함수를 호출 할 수 있는다.
- `try` : 에러가 발생할 가능성이 있는 `throw`가 붙은 함수를 호출 할때 붙인다.
- `catch` : `do` 구문 뒤에 붙는다. `try` 호출 중에서 어느 하나라도 오류를 던지면 `catch`가 실행된다. 오류 종류를 구체적으로 정하지 않으면 던져진 error가 있다면 가리지 않고 잡아내는데, 특정 오류 종류를 잡아내기 위해 `catch`블록을 추가할 수도 있다.
