# CardGame_Step5_Feedback
### 프로그래밍 요구사항
- 7카드, 5카드에서 이긴 사람을 자동으로 계산하는 방법을 추가한다.

### 구현사항
- 포커게임 표준 족보에 따른 Hands서열대로 점수 계산로직 설계 & 개선.
- CardTable(카드게임 한 판을 형상화), ScoreChecker객체 추가
- UnitTest코드 추가 : 점수계산 로직을 만들면서 TDD의 중요성을 느꼈다. 동시에 테스트코드에 파란불이 들어오는 즐거움을 알게됐다. 사람 뿌듯하게 하는 맛이 있다!

### 수정사항
- 객체 단위로 역할 재분담
  - Card클래스에 comparable구현
  - Card나 CardStack 내부에 필요한 로직 추가함
- Hands 구조체 추가 : Hands가 동일할때 최송 승자를 가리는 방법 구현
  - 단순히 카드 Hands만 비교해서 정교한 결과를 내기가 어려웠다. 유저 중에 동일한 Hands가 나오면 그중 승자를 가려내기위해 탑카드를 알아야 되는 문제점이 있었는데, 그렇게되면 사용자의 rank가 단순히 hands만 가지는 것이 아니라 탑카드도 가지고 있어야했다. 이를 구현하기위해서 아예 Hands 구조체를 만들었던 점.


### 구현사항 - 고차함수 reduce()
> pair를 검사할때 reduce()를 사용했는데, 이해하기 까다로웠어서 정리함.<br/>[(참고링크_도움을 많이 받았던 stackoverflow 글)](https://stackoverflow.com/questions/29727618/find-duplicate-elements-in-array-using-swift)

ScoreChecker에서 pair류의 Hands를 검사할 때 사용함
```Swift
// ScoreChecker.swift

private func matchPairs(_ cards: [Card]) -> [PokerHands] {
   let pairCheck = cards.reduce(into: [String:[Card]]()) {
       $0[$1.denomination.description, default: []].append($1)
       }
    var hands = [PokerHands]()
    pairCheck.forEach {
       switch $0.value.count {
       case 4: hands.append(.FourOfAKind)
       case 3: hands.append(.ThreeOfAKind)
       case 2: hands.append(.OnePair)
       default: hands.append(.NoPair)
       }
   }
   return hands
}
```
```swift
struct Contact {
    var name:String
    var phone:String
    init(name:String, phone:String) {
        self.name = name
        self.phone = phone
    }
}

// contacts 배열에서, 전화번호가 똑같은 Contact를 찾아서 전화번호를 기준으로 name을 묶고,
// 새로운 딕셔너리를 만드는 작업 -> [전화번호:[이름]]

let contacts = [
    Contact(name: "Rob",     phone: "555-1111"),
    Contact(name: "Richard", phone: "555-2222"),
    Contact(name: "Rachel",  phone: "555-1111"),
    Contact(name: "Loren",   phone: "555-2222"),
    Contact(name: "Mary",    phone: "555-3333"),
    Contact(name: "Susie",   phone: "555-2222")
]

let crossCheck = contacts.reduce(into: [String: [String]]()) { result, contact in
    result[contact.phone, default: []].append(contact.name)
}

/*
 crossCheck는 contacts를 [String: [String]]타입으로 변경한 결과
   - reduce(into: 변경 후 형태)

 contacts를 [String: [String]]타입으로 변경하는 로직은 클로저 내부에 있음
  - 클로저 내부 설명
  result는 'into: 타입'에서의 '타입'을 상징하는 매개변수
  contact는 contacts의 하나의 값(for contact in contacts할때의 하나의 value)
  result[contact.phone, default: []]에서 앞쪽의 contact.phone은 result[key]를 상징하고, default:[]는 딕셔너리 value의 초기값 (딕셔너리의 value는 배열 형태이므로)
  append는 value값을 채워넣는 동작
 */

// 더 줄인 클로저를 연습해봤다!
let crossReference = contacts.reduce(into: [String: [String]]()) {
    $0[$1.phone, default: []].append($1.name)
}

/*
 < 설명 >
- crossReference는 contacts에 reduce동작을 한 결과값을 저장하는 변수
- crossReference의 형태는 [String: [String]]가 될 것이다
- $0은 contact(contacts배열 중의 하나의 value)
- $1: reduce()동작으로 인해 결과가 되는 값을 나타내는 $1.
  - 딕셔너리의 key값은 contact의 phone속성 (phone값을 기준으로 중복되는 value를 모은다)
  - 전화번호 중복으로인해 모여지는 value는 배열 형태로 저장되며, contact의 name속성이다.
*/
```


### 수정사항 - 객체 역할 재분담
> 5단계 미션을 진행하면서 제일 중요하고 많이 배운 부분이었다. <br/>B라는 객체가 동작을 하기위해서는 A객체의 a속성이 필요하다고 가정했을때, 나는 a속성의 접근자를 `private (set)`으로 두고 B에서 a에 접근해서 동작하게 만들었었다. <br/> 하지만 a를 `private`으로 변경하고 B에서 하던 그 동작을 A가 하는 구조로 **의존성을 줄이면, 자신이 자신이 가진 속성에만 접근해서 '일'을 하고 결과만 외부에 넘겨주는** 깔끔한 구조가 된다!

#### Card, CardStack, ScoreChecker
ScoreChecker구조체는 역할 상 Card의 수와 모양을 알아야한다. 이를위해 CardStack과 Card의 속성을 `private(set)`으로 한 게 몇몇개 있었는데, 그 코드가 아래와 같이 바뀌면서 세 구조체간 책임이 재분배되었다.

#### 변경 전

```Swift
// Card.swift

class Card: CustomStringConvertible {
    private(set) var suit: CardDeck.Suit
    private(set) var denomination: CardDeck.Denomination
...
}

// CardStack.swift

struct CardStack: CustomStringConvertible {
    private(set) var cards = [Card]()
    var description: String {
        return cards.description
    }
...

// ScoreChecker.swift

struct ScoreChecker {
...

   private func sortCards(_ cardStack: CardStack) -> [Card] {
        let cards = cardStack.cards
        let sorted = cards.sorted(by: { $0.denomination.rawValue < $1.denomination.rawValue })
        return sorted
    }
    // cardStack.cards로 접근하고, $0.denomination.rawValue처럼 Card 속성에 직접 접근

    private func isStraight(_ cards: [Card]) -> Bool {
        var count = 0
        for i in 1..<cards.count {
            if (cards[i-1].denomination.rawValue + 1) == cards[i].denomination.rawValue {
                count += 1
            }
        }
        return count >= 4
    }
    // rawValue를 비교하는 것도 Card 내부에서 cards[i-1]와 cards[i] 를 비교하도록 변경해야함
}
```
- 문제점
  - Card와 CardStack의 속성이 `private(set)`으로 열려있다.
  - ScoreChecker객체 내에서 위의 두 속성에 직접 접근해서 비교하는 동작을 한다.
    - `sortcards()` : cardStack.cards로 접근하고, $0.denomination.rawValue처럼 Card 속성에 직접 접근
    - `isStraight()` : rawValue를 비교하는 것도 Card 내부에서 cards[i-1]와 cards[i] 를 비교하도록 변경해야함

#### 변경 후
- Card에 Comparable구현
- enum Suit과 Denomination에 Comparable프로토콜 구현, rawValue-1 값 비교 메소드 구현
- sort기능 CardStack내부로 이동
- Hands구조체 설계 시 todo-list

##### Card, Enum Suit, Enum Denomination
Card에 Comparable구현하고 ScoreChecker에서 점수계산 시 필요한 정보를 Card 내부에서 계산해서 결과를 ScoreChecker로 리턴해줄 수 있도록 `isContinuous()`나 `isSameSuit()`같은 메소드를 구현했다.

```Swift
// Card.swift

class Card: CustomStringConvertible, Comparable {
    private var suit: CardDeck.Suit
    private(set) var denomination: CardDeck.Denomination

    ...

    static func <(lhs: Card, rhs: Card) -> Bool {
        return lhs.denomination.rawValue < rhs.denomination.rawValue
    }

    static func ==(lhs: Card, rhs: Card) -> Bool {
        return lhs.denomination == rhs.denomination
    }

    func isContinuous(previous: Card) -> Bool {
        return self.denomination.isContinuous(previous: previous.denomination)
    } // 여기서도 denomination속성의 rawValue를 가져와서 비교하는 것이 아니라 denomination스스로의 값이 연속되는지 검사하는 로직을 구현했다.

    func isSameSuit(previous: Card) -> Bool {
        return self.suit == previous.suit
    } // suit도 마찬가지로 내부에 비교 동작 구현 - Comparable구현

}

// CardDeck.swift

extension CardDeck {

    enum Suit: String, EnumCollection, Comparable {
        case heart = "♥️"
        case diamond = "♦️"
        case clover = "♣️"
        case spade = "♠️"

        static func <(lhs: CardDeck.Suit, rhs: CardDeck.Suit) -> Bool {
            return lhs.hashValue < rhs.hashValue
        }

        static func ==(lhs: CardDeck.Suit, rhs: CardDeck.Suit) -> Bool {
            return lhs.hashValue == rhs.hashValue
        }
    }

    enum Denomination: Int, CustomStringConvertible, EnumCollection, Comparable {
        case one = 1, two, three, four, five, six, seven, eight, nine, ten, eleven, twelve, thirteen

        var description: String {
            switch self {
            case .one: return "A"
            case .eleven: return "J"
            case .twelve: return "Q"
            case .thirteen: return "K"
            default: return String(self.rawValue)
            }
        }

        static func <(lhs: CardDeck.Denomination, rhs: CardDeck.Denomination) -> Bool {
            return lhs.rawValue < rhs.rawValue
        }

        static func ==(lhs: CardDeck.Denomination, rhs: CardDeck.Denomination) -> Bool {
            return lhs.rawValue == rhs.rawValue
        }

        func isContinuous(previous: CardDeck.Denomination) -> Bool {
            return (self.rawValue - 1) == previous.rawValue
        }
    }

}

```

##### CardStack
sorting기능을 stack내부에 구현

```swift
// CardStack.swift

struct CardStack: CustomStringConvertible {
    private var cards = [Card]()

  ...

    func sort(_ cardStack: CardStack) -> [Card] {
        return cards.sorted()
    } // Card에 comparable을 구현했더니 sorted()를 사용할 수 있음
}

```
##### Hands객체 설계했던 내용

- 속성: PokerHands enum, Card(TopCard)
- 사용자가 가진 hands별로 TopCard가 있어야하고, 그 hands중에서도 제일 큰 점수를 가진 hands의 탑카드만 유효한의미임.
- 따라서 [PokerHands]로 점수를 계산하고 튜플형태로 탑카드를 가지고있던걸 비교하니까 비교하고 맞는 값을 가져오기 힘들었다.
  - onepair 1개(2하트), nopair 3개(10하트,5클로버,6다이아)인 5스터드에서 onepair의 탑카드인 2하트의 가중치점수가 가져와져야하는데 nopair중의 탑카드인 10의 가중치점수가 가져와져서 계산됨.
- 의미있는 hands의 탑카드로 점수를 계산할때 가중치를 곱한다.
- 점수계산하는 객체에서는 기존에[PokerHands]로 하던걸 [Hands]객체로 비교. comparable구현해서 [Hands]를 sort하고 가장 큰 페어(or 플러시,스트레이트)의 탑카드로 최종 스코어에 가중치를 곱해준다.
- 속성을 괄호에 두개 넣어서 배열로 넣은걸 형상화한다면 아래와 같음.
  - hands배열: [(PokerHands:TopCard),(PokerHands:TopCard),(PokerHands:TopCard)]: 한사람이 이렇게 hands배열을가지고있고, **ScoreChecker에서 final점수계산할때 이 [hands]를 가지고 계산하면될듯.**
(만약에 이렇게 구현하면 모든 유저가 다 노페어일때 탑카드로 비교할수있게됨.)
- 전부 다 노페어인경우는 제일 큰 탑카드를 가진 노페어로 계산한다.
- 스트레이트나 플러시인경우는, 탑카드를 구하는 방법을 따로 만든다.
  - hands객체의 PokerHands속성이 스트레이트면 스트레이트 패의 탑카드를 구하고(이건 ScoreChecker에서 스트레이트플러시체크하는데서해야함)
  - PokerHands의 속성이 플러시이면 suit을 비교해서 탑카드를 구한다.

***

#### 추가: 객체역할 재분담에 대해 TIL에 정리한 내용들
> JK에게 질문하면서 알게 된 점을 되게 러프하게 쓴거라, 다소 정리가 안되고 위의 내용과 중복된 것들이 있지만 당시에 바로 질문하고 정리한게 제일 구체적으로 쓴것같아 붙여넣었음!


- getter메소드 구현할때 네이밍
  - get을 쓰지말라는뜻은 메소드 이름에 get을 쓰지 말라는게 아니라 getter를 만든다는것 자체가 속성값을 가져와서 다른 곳에서 어떠한 동작을 하고 비교를 한다는 뜻을 내포하고있기때문에, `card.isSameSuit()`처럼 내부에서 비교하고 결과를 리턴해주는 메소드가 있어야한다는것. 다만 Card클래스에서 구현한 comparable 메소드를 구현할때 아래처럼 hashValue나 rawValue에 접근했는데, 이것또한 상위개념인 Card클래스에서 suit나 denomination의 속성값을 가져와서 쓰는거니까, suit나 denomination속성에서 comparable을 구현하고 Card클래스의 메소드에서 `.hashValue / .rawValue`로 접근했던것을 지울 수 있다.
  ```Swift
  // Before
  // Card.swift
  func isSameSuit(previous: Card) -> Bool {
  return self.suit.hashValue == previous.suit.hashValue
  }

  // After add Comparable to enum suit

  // suit에서는 hashValue에 접근하여 메소드 구현해도 됨.
  enum Suit: String, EnumCollection, Comparable {
      case heart = "♥️"
      case diamond = "♦️"
      case clover = "♣️"
      case spade = "♠️"

      static func <(lhs: CardDeck.Suit, rhs: CardDeck.Suit) -> Bool {
          return lhs.hashValue < rhs.hashValue
      }

      static func ==(lhs: CardDeck.Suit, rhs: CardDeck.Suit) -> Bool {
          return lhs.hashValue == rhs.hashValue
      }

  // Card.swift
  func isSameSuit(previous: Card) -> Bool {
     return self.suit == previous.suit
  }
  ```

  ```swift
  // Before
  // Card.swift
  func isContinuous(previous: Card) -> Bool {
    return (self.denomination.rawValue -1) == previous.denomination.rawValue
  }
  // ScoreChecker.swift
  private func isStraight(_ cards: [Card]) -> Bool {
      var count = 0
      for i in 1..<cards.count {
          if cards[i].isContinuous(previous: cards[i-1]){
              count += 1
          }
      }
      return count >= 4
  }

  // After
  // CardDeck.swift
  enum Denomination: Int, CustomStringConvertible, EnumCollection {
      case one = 1, two, three, four, five, six, seven, eight, nine, ten, eleven, twelve, thirteen

      var description: String {
          switch self {
          case .one: return "A"
          case .eleven: return "J"
          case .twelve: return "Q"
          case .thirteen: return "K"
          default: return String(self.rawValue)
          }
      }

      // Denomination에도 이렇게 내부에 `rawValue값 - 1`로 비교하는 메소드를 만들고
      func isContinuous(previous: CardDeck.Denomination) -> Bool {
          return (self.rawValue - 1) == previous.rawValue
      }
  }
  //Card.swift
  // Card에서는 denomination의 rawValue까지 접근하지않고,
  // ScoreChecker에서 넘겨받은 card를 denomination까지만 접근해서 Enum
  // `denomination.isContinuous`에 인자를 넘긴다.  
  func isContinuous(previous: Card) -> Bool {
    return self.denomination.isContinuous(previous: previous.denomination)
  }

  ```
