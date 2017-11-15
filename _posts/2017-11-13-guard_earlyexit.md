---
layout: post
title: guard의 효용 - early exit!
tags: guard swift
categories: Swift
---



### guard의 사용에 대해서

**guard는 else문에서 빠져나가는 동작을 할때 효용이 있다.**

guard를 쓰던 곳에서 원래 하던 return을 안하니 (= 함수를 빠져나가버리질 않으니) 에러가 났다. 알아보니 else문에 return이 없어서 나는 에러였는데, 내 코드는 else에서 return을 해버리면 안되는 로직이었다. 

- 만약 guard (혹은 guard - let)를 쓴다면 else문에서 함수를 빠져나가는 동작이 있을때만 효용이 있음 : guard 바로 뒤의 구문이 만족되지 않으면 그냥 함수를 종료시켜버리는 로직에만 효과가 있다는 뜻
- else문에서 함수를나가지 않고 다음 구문을 실행하야 되는 로직의 경우는 if (혹은 if- let)을 써야함 

예제 코드는 아래에!



##### <1>

사다리 연습중에 if-else를 사용하는 대신 guard를 사용하라는 미션이 있어서 guard를 사용해보았다. 사용했던 함수는 사다리 중간의 randomBar를 만드는 동작에서 처음엔 String을 리턴하는 함수였다. 

~~~swift
//리턴값이 있던 전 함수. 에러가 없었음
mutating func makeRandomBar() -> String {
  for _ in 1..<players.count {
    guard Int(arc4random_uniform(2)) == 0 else {
         return "     "
    }
    return "-----"
  }
}
~~~



##### <2>

그런데 리팩토링을 하면서 `String`을 리턴하는 대신에 `[Bool]`타입의 어레이에 `Bool`값을 추가해줘야 하는 방식으로 바뀌게 되었고 guard에서는 에러가 났다.  **> else에서 함수가 끝나지 않으니까 나는 에러였음.**

~~~swift
 //사다리 한 층에서 사이사이 bar를 랜덤으로 만드는 함수.
    mutating func makeRandomBar() {
        for _ in 1..<players.count {
            guard Int(arc4random_uniform(2)) == 0 else {
                randomBars.append(false)
            }
            randomBars.append(true)
        }
    }
/*
에러메시지
'guard' body may not fall through, consider using a 'return' or 'throw' to exit the scope
*/
~~~

