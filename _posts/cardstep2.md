# CardGame Step2

### 수정사항
- OutputView에서 showCard()와 resultMessage()를 showResult()로 통합
- GameMenu에 사용자 Input을 대신하는 Enum추가
- EnumCollection 사용


### 1. OutputView - showResult()
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



### 2. GameMenu Enum

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
