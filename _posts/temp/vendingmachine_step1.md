### 구현사항
- Beverage 클래스 구현
- Milk, SoftDrink, Coffee 클래스 추가하고 Beverage와 상속관계로 구현
- `DateFormatter()`사용

### 수정사항
- 슈퍼클래스 Beverage 속성의 접근자를 `private`으로 수정, `super.init()`
- MyDate객체 생성

### 구현사항 1. Beverage와 음료 클래스 상속관계 구현
모든 음료 클래스가 기본적으로 동일한 속성과 기능을 갖고있어서 하나의 슈퍼클래스로 추상화했다.<br/>
**상속관계에서 서브클래스에만 있는 특정한 속성을 어떻게 초기화하는지** 알 수 있었다.<br/>
(아래 Milk 코드의 flavor속성과 init 참고)

```Swift
// Beverage.swift

class Beverage: CustomStringConvertible {
    var type: String {
        return "음료"
    }
    var brand: String
    var weight: Int
    var price: Int
    var name: String
    var MFDDate: Date

    var description: String {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyyMMdd"
        let MFDFormatted = dateFormatter.string(from: self.MFDDate as Date)
        return "\(type) - \(brand), \(weight)ml, \(price)원, \(name), \(MFDFormatted)"
    }

    init(brand: String, weight: Int, price: Int, name: String, MFDDate: Date) {
        self.brand = brand
        self.weight = weight
        self.price = price
        self.name = name
        self.MFDDate = MFDDate
    }
}

// Milk.swift

class Milk: Beverage {
    private var flavor: String
    override var type: String {
        return "\(flavor)우유"
    }

    // 우유 객체는 어떤 맛의 우유인지 표현해야해서 한가지 속성이 더 있음 - flavor
    init(flavor: String, brand: String, weight: Int, price: Int, name: String, MFDDate: String) {
        self.flavor = flavor
        super.init(brand: brand, weight: weight, price: price, name: name, MFDDate: MFDDate)
    }
}
```

### 구현사항 2. DateFormatter() 사용
날짜를 표현하는 `Date()`객체를 원하는 포맷으로 출력하기위해 `DateFormatter()`를 사용한다.<br/>
코드에서는 입,출력에서 모두 사용했으며 원하는 포맷을 String으로 입력해서 사용할 수 있다.

```Swift
// main.swift
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyyMMdd"

let strawberryMilk = Milk(flavor: "딸기",
brand: "서울우유",
weight: 200,
price: 1000,
name: "날마다딸기우유",
MFDDate: dateFormatter.date(from: "20171009") ?? Date())

// Beverage.swift
let MFDDate = Date()
var description: String {
    let dateFormatter = DateFormatter()
    dateFormatter.dateFormat = "yyyyMMdd"
    let MFDFormatted = dateFormatter.string(from: self.MFDDate as Date)
    // DateFormatter의 dateFormat의 지정된 포맷으로 Date타입 객체를 String으로 변경
    return "\(type) - \(brand), \(weight)ml, \(price)원, \(name), \(MFDFormatted)"
}
```
- `MFDDate: dateFormatter.date(from: "20171009") ?? Date()` 형태로 사용해서 `Date()`객체를 만든다.
- DateFormatter의 dateFormat속성을 "yyyyMMdd"형태로 지정했으니 순서에만 맞에 초기화해주면 년도, 월, 일을 문자열의 맞는 자리에 넣어서 `Date()`를 생성해준다.
- ***여기서 yyyyMMdd문자열의 대,소문자는 정해진 포맷이니 임의로 변경하면 값이 들어가지 않는다!***

### 수정사항 1. 슈퍼클래스와 서브클래스, `super.init()`
슈퍼클래스인 Beverage의 속성의 접근자가 open access였다가 `private(set)`로 수정했는데, **서브클래스가 슈퍼클래스의 속성에 직접 접근하지않고 메소드로 접근해서 사용하라는 피드백을 받았다.**<br/>
이것도 또한 일반적으로 객체 내부의 속성에 접근해서 외부에서 비교하지말고, 속성을 가지고 동작하는 메소드를 객체 내부에 위치시켜서 스스로 동작하게하는 캡슐화와 상통하는 의미라고 생각했다.  
