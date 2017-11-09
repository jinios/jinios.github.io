---
layout: post
title: 타입메서드 
tags: static Swift
categories: Swift
---

### 타입메서드

타입메서드는 인스턴스를 생성하지 않고도 사용할 수 있는 메서드를 말한다. (자바로 치면 static메서드)

struct의 타입메서드는 static 키워드를 붙인다. 

~~~swift
struct Square {
  static func numberOfSides() -> Int{
    return 4
  }
}
~~~

class의 타입메서드에는 class 키워드를 붙인다.

~~~swift
class Zombie: Monster {
  class func makeNoise() -> String {
    return "Brains..."
  }
}
~~~

타입메서드를 사용하기 위해서는 타입 자체에 다음처럼 호출하면 된다.

~~~swift
let sides = Square.numberOfSides()
let noise = Zombie.makeNoise()
~~~

##### 서브클래스에서의 오버라이드

서브클래스는 클래스메서드를 자체적으로 구현 (오버라이드)할 수 있다.

~~~swift
class GiantZombie: Zombie {
  override class func makeNoise() -> String {
    return "rrrr...!!"
  }
}
~~~

 여기서 GiantZombie는 Zombie의 서브클래스이며, class메서드의 오버라이드를 제공하고있다. (makeNoise()) 만약 class메서드가 오버라이드되지 않도록 하고 좀비가 모두 같은 소리를 낸다고 하면 어떻게 처리해야할까? 

~~~swift
//Zombie 클래스의 해당 메서드를 static키워드 처리해주면 된다.

class Zombie: Monster {
  static func makeSpookyNoise() -> String {
    return "Brains..."
  }
}
~~~

static 키워드를 지정하면 makeSpookyNoise() 메서드의 서브클래스 버전을 제공하지 않겠다는 뜻이 된다! **static 대신 final class를 사용해도된다.**

##### 타입메서드의 수준 (시야?)

타입메서드는 타입 수준의 값을 처리할 수 있다. 다른 타입메서드를 호출 할 수 있으며, 타입 프로퍼티도 처리할 수 있다. **하지만 타입메서드는 인스턴스의 값을 처리할 수는 없다.** 타입메서드는 인스턴스메서드를 호출 할 수 없으며, 인스턴스 프로퍼티도 처리할 수 없다. (인스턴스가 타입보다 더 narrow하고 strict하기때문에 타입 수준에서 처리 할 수 없음 - 타입 수준은 인스턴스가 없어도 접근이 가능한데 인스턴스는 인스턴스가 반드시 필요하기 때문.)





