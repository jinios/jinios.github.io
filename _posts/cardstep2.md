# CardGame Step2

### 구현사항
- CardDeck에 기본 메뉴 기능구현
- Enum - `static let allValues`

### 수정사항
- OutputView에서 showCard()와 resultMessage()를 showResult()로 통합
- GameMenu에 사용자 Input을 대신하는 Enum추가
- EnumCollection 사용 // wiki정리 필요

### 구현사항 1. CardDeck
> CardDeck에 기본메뉴 기능을 구현함 - count(), shuffle(), removeOne(), reset()

#### CardDeck의 속성에 대한 고민
CardDeck은 카드 52장을 초기화하고, 섞고, 한 장 뽑고, 현재 가지고있는 카드 수를 세는 기능을 한다. <br/>
CardDeck의 책임과 기능으로 본다면 `[Card]`를 속성으로 가지고있어야하는데, 그 속성의 상태를 어떤식으로 바꾸느냐가 고민되었다. CardDeck의 동작에 따라 `[Card]`가 바뀐다면, 그 메소드들은 결국 자신의 속성을 계속 바꾸니까 mutating동작을 계속 하게된다는게 문제가 될 것 같았다. [(Class에서는 mutating키워드가 없어도 되지만_관련링크!)](https://stackoverflow.com/questions/38422781/mutating-function-inside-class)<br/>
(객체 자신의 속성을 내부에서 변경한다는 뜻으로) 특히 수업때나 여기저기서 mutating은 되도록 안쓰는게 좋는 말을 들어서, mutating을 쓰지 않는 방법으로 고민해봤는데 **만약 그 객체의 속성이 변경되는 것 때문에 새 속성이 만들어지는대로 객체를 새로 만드는게 더 비효율적이었다.** 만약 이 방법으로 한다면 CardDeck의 동작을 호출하는 상위모듈에서 속성이 바뀌는 동작을 할 때마다 CardDeck객체를 새로 만들어야 의도한대로 동작할것같았다. <br/>
mutating은 만약 남용하면 부정적인 측면이 있어서 되도록 남용되는 것은 권장되진않지만 만약 mutating을 쓰는것이 더 효율적인 경우 사용해도된다고해서, **CardDeck객체 내부에서 속성을 변경하기로했다.**

#### CardDeck의 reset()에 대한 고민
현재는 새로운 CardDeck()객체를 리턴하는 역할. <br/>
CardDeck은 init()이 없이 빈 카드 배열로 초기화가 되고, reset()에서 카드의 suit과 denomination대로 52개의 카드객체가 만들어져서 배열에 담는 작업을 하는 식으로 설계했다. 이렇게 되니 CardDeck객체를 만들고 바로 shuffle을 하면 `0장의 카드를 섞는` 이상한 동작을 했다.. 그래서 CardDeck은 항상 객체 생성 때부터 52장의 `[Card]`를 가지도록 init()을 만들었다. 이렇게 하니 reset()과 init()이 이름만 다르지 로직 코드가 완전히 동일해져버렸고, 심지어 상위모듈에서 reset()을 호출하면 `[Card]`를 리턴해버리니까 warning이 뜬다.
**해결: reset()에서의 리턴값을 CardDeck()으로 만듦!**

#### Shuffle array in Swift
CardDeck의 `shuffle()` 구현을 위해서는 Array를 섞는 동작이 필요했다. 그러나 스위프트엔 Array를위한 shuffle 메소드를 제공하지 않는다...결국 [Fisher-Yates알고리즘](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle) 사용하여 쉽게 구현할 수 있었다! 정말 쉽고 좋게 짰는지는 확신할 수 없지만 알고리즘으로 직접 무언가를 만들어낸적이 처음이라 뿌듯했다 :)

### 구현사항 2. Enum interaction
> enum의 케이스들을 순환해야했는데, (또)스위프트는 enum을 iterate하는 기능을 제공하지 않는다고 한다. (...)

조금 억지같은 느낌으로 할 수 있는 방범이 있었다. 바로 `static let allValues` [(참고링크)](https://stackoverflow.com/questions/24007461/how-to-enumerate-an-enum-with-string-type/24137319#24137319)
```swift
enum Suit: String {
    case heart = "♥️"
    case diamond = "♦️"
    case clover = "♣️"
    case spade = "♠️"

    static let allValues = [heart, diamond, clover, spade]
}
```

사용은 이런 식으로 할 수 있다.
```swift
    ...
    for shape in Suit.allValues {
      for number in Denomination.allValues {
        cards.append(Card(suit: shape, denomination: number))
      }
    }  
    ...
```


### 수정사항 1. OutputView - showResult()
#### Before Code Review
```swift
// OutputView.swift

    func showCard(cardToPrint: Card) {
        print(cardToPrint)
    }

    func resultMessage(text: String) {
        print(text)
    }
  ```
같은 동작이지만 showCard()는 NSObject를 상속받고 `description`을 오버라이드한 Card클래스의 `description`을 출력하는 함수였다. (Card가 NSObject를 상속받아서 description을 쓴 이유는 출력할 때 `card`만 인자로 넘기면 되기 때문. (`card.description`할 필요가 없음)) <br/>
<br/>
하지만 효율성으로 봤을 때 두 함수를 통합하는 것이 훨씬 보기 좋을 것 같았고 resultMessage()함수가 굳이 String을 인자로 받아서 print()동작을 똑같이 실행하는 것이 괜찮은지에 대한 아쉬움이 있었다.<br/>
그리고 JK의 힌트! `그럼 이건 어때요? func resultMessage(text: CustomStringConvertible)`


#### After Code Review
- showCard()와 resultMessage()를 showResult()로 통합:
  - resultMessage()는 대부분 프로그램 내의 enum과 인스턴스의 description을 프린트하고있었다.
  - Card는 CustomStringConvertible을 상속하도록 변경
  - result를 보여주는 함수는 인자로 CustomStringConvertible을 받도록 변경
  - **프로토콜에 의존하게 만들어서, 인터페이스 구조 사용**

```Swift

// Card.swift

class Card: CustomStringConvertible {
    private var suit: CardDeck.Suit
    private var denomination: CardDeck.Denomination
    var description: String {
        return self.suit.rawValue + self.denomination.description
    }

// OutputView.swift

    func showResult(text: CustomStringConvertible) {
        print(text)
    }    

```
변경 후 showResult()는 `CustomStringConvertible` 프로토콜에 의존성을 갖게되며, 프로그램 내부에서 출력이 필요한 모든 객체는 `CustomStringConvertible`을 상속하게하여 재사용성을 높일 수 있게 되었다.



### 수정사항 2. GameMenu Enum

#### Before Code Review
`userInput: String`을 그대로 switch-case문에서 비교하여 로직 실행
```swift
let userInput = InputView().askUserInput(text: OutputView.programDescription.chooseMenu.description)

  switch userInput {
  case "" :
    // doSomething()
  case "1" :
    // doSomething()
  case "2" :
    // doSomething()
  case "q" :
    // doSomething()
      break
  default : // doSomething()
```
입력한 값을 enum 타입으로 바꿔서 활용하는 것을 권장받았다. 하드코딩하는 숫자나 문자열을 의미있는 코드로 표현하는 게 실수도 덜할 수 있고, 명확하게 표현가능하고, 수정할 때도 편리하기때문에! <br/>
특히 switch-case로 비교할때 enum을 이용하면 사용자의 input을 제한한다는 의미도 있지만 컴파일러가 비교구문에서 빠진 케이스를 미리 알려줄 수 있기 때문에 개발자의 실수를 방어하는데에도 용이한 면이 있다.<br/>

input값을 enum으로 제한하기 위해, **enum의 rawValue를 이용해서 그 enum의 특정한 케이스(rawValue와 일치하는)가 만들어지는 방법**을 사용했다.
