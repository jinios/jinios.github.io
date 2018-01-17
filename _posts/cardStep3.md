# CardGame Step3 피드백

### 수정사항
- CardDeck수정 : 카드덱에 카드 여러 장을 한꺼번에 배열로 뽑아내는 메서드 추가
- CardStack구조체 : 카드스택 구성 - 사이즈가 1~7인 카드배열을 가지는 CardStack 구조체 구현

### 1. CardStack의 속성
요구사항이 `카드덱을 활용해서 1장의 카드가 쌓인 스택과 7장의 카드가 쌓인 스택을 출력하도록 수정한다.`였다. <br/>
그래서 CardStack으로 스택 구조체를 만들면서 스택 구조체는 속성으로, 사이즈가 1~7인 `[Card]`를 가져야 한다고 생각했었다.<br/>
하지만 게임 진행 상 **스택은 카드가 쌓인 것 그 자체를 의미하므로, 사이즈가 1~7인 스택은 모두다 다른 스택 인스턴스** 라는 것을 깨닫고 코드를 고치게 되었다.

#### Before Code Review
```swift
struct CardStack {
    var oneCard: [Card]
    var twoCards: [Card]
    var threeCards: [Card]
    var fourCards: [Card]
    var fiveCards: [Card]
    var sixCards: [Card]
    var sevenCards: [Card]
}
```
~~(지금 보면 참 말도 안되는 코드라고 생각한다ㅠㅠ)~~

#### After Code Review
- CardStack은 `[Card]`를 속성으로 가진다.
- `makeCards()`: CardDeck에서 `removeOne()`함수를 활용하여, `[Card]`를 만든다.
- `makeStack()`: CardDeck에서 `[Card]`를 이용하여, CardStack객체를 만든다.
- main에서는 스택에 쌓을 카드 수를 CardDeck의 `makeCards()`함수에 넘겨준다.


```Swift
// CardStack.swift

struct CardStack: CustomStringConvertible {
    private var cards = [Card]()
    var description: String {
        return cards.description
    }

    init(_ cards: [Card]) {
        self.cards = cards
    }
}

// CardDeck.swift

func makeCards(_ countOfCards: Int) -> [Card] {
        var cards = [Card]()
        for _ in 0..<countOfCards {
            let picked = removeOne()
            cards.append(picked)
        }
        return cards
    }

    func makeStack(cards: [Card]) -> CardStack {
        return CardStack(cards)
    }
...

// main.swift

for i in 1...7 {
    let cards = cardDeck.makeCards(i)
    let stack = cardDeck.makeStack(cards: cards)
    ...
}

```

### 2. struct property 출력
struct의 속성을 그냥 출력하면 해당 속성값이 나온다! (주소값이 나오는게 아니라)<br/>
예를들어 이렇게 나온다는 뜻. `print(구조체인스턴스.속성)`하면 아래의 화면처럼 콘솔에 찍혔다. 어떤 인스턴스의 어떤 속성인지까지 아주 친절히 알려준다.

```
CardStack(cards: [♣️10])
CardStack(cards: [♥️4, ♦️6])
CardStack(cards: [♣️9, ♥️3, ♥️Q])
CardStack(cards: [♠️10, ♠️A, ♠️4, ♥️K])
CardStack(cards: [♣️2, ♠️7, ♣️5, ♥️5, ♦️K])
CardStack(cards: [♣️3, ♦️4, ♦️J, ♣️Q, ♠️5, ♥️6])
CardStack(cards: [♣️4, ♥️8, ♠️K, ♣️J, ♣️8, ♠️Q, ♦️10])
```
만약 CardStack이 클래스였다면 아마 주소값이 찍혔을 것이다. 이렇게 출력이 되는 이유는 스트럭트의 고유한 특징때문이라고한다.
스트럭트는 클래스와 다르게 값(Value)이다. 정확히, 값의 타입을 정의하기 위해 사용한다. 객체(Object) 레퍼런스 타입을 정의하는 class 와는 다르다. struct 로 구현된 타입의 가장 중요한 특징은 인스턴스가 값(Value)이라는 점이다. 값이라는 표현은 '고유한 메모리에 데이터가 저장된다' 라고 기계적인(?) 설명이 가능하다.
똑같은 스트럭트와 클래스의 value 특징에의해서 class는 mutating이 안되고 스트럭트는 되는 경우가 있는 것 같다.
[출처_완전좋은_스트럭트,클래스 비교 글](http://seorenn.blogspot.kr/2016/04/swift-class-struct.html)



### 3. while 0<index {}
  비교문에서 ++가 되지 않는 경우. for는 변수가 있으니 가능한데 변수가 하나씩 증가하면서 반복해야하는경우는 while이 부적절? 한지아닌지 조사하고 정리하기
