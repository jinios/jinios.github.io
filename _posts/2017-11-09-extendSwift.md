---
layout: post
title: 클래스 vs 구조체의 차이점 - 상속  
tags: class struct 상속 Swift
categories: Swift
---



>상속은 클래스와 구조체를 구분할 때의 대표적인 차이점이다.

### 상속

- 상속은 구조체와 다른 클래스만의 특징이다.
- 상속의 의미는 한 클래스가 다른 클래스 안에서 정의되는 관계를 말한다.
- 서브클래스 / 슈퍼클래스 : 서브클래스는 슈퍼클래스의 프로퍼티와 메소드를 상속한다. 

##### Monster / Zombie / Town 예시

```swift
//Town struct
struct Town {
    var population = 5_422
    var numberOfStopLights = 4
    
    func printDescription() {
      print("인구: \(myTown.population)", "가로등 수: \(myTown.numberOfStopLights)")
    }
  
    mutating func changePopulation(by amount: Int) {
        population += amount
    }
}


//Monster class
class Monster {
    var town: Town?
    var name = "Monster"
    
    func terrorizeTown() {
        if town != nil {
            print("\(name)이 도시를 공격중이다! ")
        } else {
            print("\(name)은 아직 공격할 도시를 찾지 못했다!")
        }
    }
}


//Zombie class
class Zombie {
  var walksWithLimp = true
  
  override func terroizeTown(){
    town?.changePopulation(by: -10)
    super.terrorizeTown()
  }
}

/*
Monster를 상속받은 Zombie는 Monster의 모든 프로퍼티와 메소드를 상속받는다.
상속받은 메소드를 Zombie 내에서 override해서 기능을 더 추가/변경한다.
*/
```







### 