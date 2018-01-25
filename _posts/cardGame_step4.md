# CardGame_Step4_Feedback
### 프로그래밍 요구사항
- 포커 딜러 선택을 위한 새로운 입력 메뉴를 만든다.
  - 게임은 7카드스터드 방식과 5카드스터드를 지원하며 메뉴로 선택한다.
- 참여자는 딜러를 제외하고 1명에서 4명까지 참여할 수 있다.
- 카드게임 종류와 참여자 수를 입력받는 GameInputView 구조체를 구현한다.
- 카드가 남은 경우는 계속해서 게임을 진행하고, 카드가 부족할 경우 종료한다.

### 구현사항
- Player와 Dealer 구조체 생성, 상속
- ResultData구조체 생성

### 수정사항
- main의 코드 줄임 : GameController추가
- OutputView의 showResult()인자에 description제거
- GameController의 init() throws로 수정
- enum의 rawValue와 Associated Value같이쓰기?(미션에 적용은 안했지만 새로 알게된 것)
- 에러수정 - Range error

***

### 구현사항 \#1 : Player와 Dealer 구조체 생성, 상속
Dealer와 Player의 속성이 동일하고, Dealer가 더 추가적으로 하는 동작이 많아서 Player를 상속하는 식으로 구현했다. (Player <- Dealer) <br/>
딜러는 플레이어의 속성인 stack을 가질 수 있으며, 딜러의 stack은 `Dealer().stack`으로 사용 할 수 있다.

```swift
// Player.swift

class Player: CustomStringConvertible {
    private(set) var stack: CardStack
    private var position: Int?

    var description: String {
        return "참가자#\(position!) " + stack.description
    }

    init(stack: CardStack, position: Int?) {
        self.stack = stack
        self.position = position
    }
}

// Dealer.swift

class Dealer: Player {
    override var description: String {
        return "딜러 " + stack.description
    }

    init(stack: CardStack) {
        super.init(stack: stack, position: nil)
    }

}
```
#### 알게 된 사실: 상속에서의 private 접근제어자
스위프트에서는 자바의 `protected`와 같은 접근자가 없다. <br/>

슈퍼클래스인 Player의 stack속성을 `private`으로 감추고싶었는데, `private`으로 설정하면 서브클래스인 Dealer가 해당 속성에 접근할 수 없다. <br/>
(스위프트의 `private`는 '동일 파일 내에서만 접근이 허락되고 이 외에는 접근을 금지시킴' 이라는 의미로 사용된다.)<br/>
[스위프트 블로그](https://developer.apple.com/swift/blog/?id=11)에서 찾아보니 `protected`는 아래와 같은 이유로 제공되지 않는다. <br/>
스위프트의 접근제어자를 설계할 때 기본적으로 `private`과 `internal`의 기준으로 설계했다고 한다. <br/>
(`internal`은 디폴트 엑세스레벨로, '해당 멤버나 클래스는 사용되는 프로젝트의 타겟에 소속된 곳이라면 어디서든 접근이 허락된다', '같은 모듈 내에서만 접근을 허용한다' 라는 의미.)  <br/>

이와는 대조적으로 `protected`는 상속을 통해 완전히 새로운 제어 수준을 만들어낸다고 할 수 있다. 서브클래스라면 새로운 `public`메소드나 속성을 통해서 언제나 `protected`한 속성에 접근 할 수 있고, 오버라이드도 가능해서 꼭 보호가 된다고도 할 수 없지만, 또 상속관계가 아닌 다른 클래스는 접근을 제한한다. 스위프트는 이러한 `protected`의 특성을 'unnecessarily restrictive'라고 생각하는 것 같다. <br/>
또한 `extensions`과 `protected`가 함께 사용될때 얼마나 clear하게 접근을 제한할 수 있는지도 불명확하다고 설명한다. 결과적으로 여러가지 이유로 스위프트는 `protected`를 제공하지 않는다.<br/>

**대신에 `private(set)` 키워드를 사용해서 stack속성을 감추고 Dealer클래스에서도 접근할 수 있도록 수정했다.**

```Swift
class Player: CustomStringConvertible {
    private(set) var stack: CardStack
    private var position: Int?

    var description: String {
        return "참가자#\(position!) " + stack.description
    }

    init(stack: CardStack, position: Int?) {
        self.stack = stack
        self.position = position
    }

}
```


<br/> <br/>

### 수정사항 \#1 : main의 코드 줄이기, GameController추가
#### init?() / init() throws
> 로직을 확장하고 필요한 순서대로 main에서 다 호출하다보니까 main이 엄청 길어졌다.
> main의 로직을 controller객체가 담당하고 main에서는 컨트롤러만 만들어서 게임을 진행하도록 수정했는데, 이 과정에서 `init?()`과 `init() throws`를 사용해봤다.


#### init?

초반의 컨트롤러의 초기화는 아래와 같이 `init?`을 사용했다. <br/> main에서 컨트롤러 객체를 만들고 컨트롤러 내에서 입력한 studType과 player 수로 게임진행을 할지말지 결정하는게 아니라, **유효하지 않은 studType이나 룰에 어긋나는 player수가 입력되면 아예 컨트롤러 객체가 만들어지지도 않도록** 설계했다.<br/> (게임을 진행할 수 없는 무효한 값인데 컨트롤러를 만들어서 또 그 안에서 값을 판단하는게 효율적이지 않은 일이라고 생각했다.)

```Swift
// GameController.swift

  struct GameController {
    private (set) var studType: Int
    private (set) var numberOfPlayer: Int

        init?(stud: GameInputView.CardStudMenu,
              numberOfPlayer: Int?) {
            switch stud {
            case .one:
                self.studType = 7
            case .two:
                self.studType = 5
            case .quitGame:
                return nil
            case .wrongInput:
                return nil
            }
            guard let number = numberOfPlayer else {
                return nil
            }
            if number >= 0 && number <= 4 {
                self.numberOfPlayer = number
            } else {
                return nil
            }

        }
  }

// main.swift

...

let unconfirmedController = GameController(stud: stud, numberOfPlayer: inputNumber)
       guard let controller = unconfirmedController else {
           runProgram = false
           // unconfirmedController가 nil이면 게임 종료
           break
       } // unconfirmedController가 nil이 아니면 게임 진행

      ...
```


#### init() throws
`init?`의 문제점 : 이렇게 하면 상위모듈에서 컨트롤러 객체가 '만들어졌을때 / nil일때'로 단편적으로만 구분돼서, nil인데 어떤 case여서 nil인지 구분이 되지 않는다. <br/>
<U>유저의 입장에서 어떤 조건때문에 게임이 중단된건지 알아야하기때문에,</U> exception상황을 더 상세하게 구분짓기위해서 init에 throws키워드를 넣고 에러처리를 하기로했다.
[init?과 init throws중에 어떤걸 써야하는지에 관한 글](https://owensd.io/2015/07/07/init-vs-init-throws/)

```Swift

// GameController.swift

struct GameController {
    private (set) var studType: Int
    private (set) var numberOfPlayer: Int

  init(stud: GameInputView.CardStudMenu,
         numberOfPlayer: Int?) throws {
        switch stud {
        case .one:
            self.studType = 7
        case .two:
            self.studType = 5
        case .quitGame:
            throw InitError.quit // 게임 종료 메시지
        case .wrongInput:
            throw InitError.wrongMenu // 잘못된 값 입력 메시지
        }

        guard let number = numberOfPlayer else {
            throw InitError.wrongPlayerNumber // 숫자가 없음
        }
        if number >= 0 && number <= 4 {
            self.numberOfPlayer = number
        } else {
            throw InitError.wrongPlayerNumber // 플레이어 수 오류
        }
    }
}

// main.swift

func run() {
    let gameInput = GameInputView()
    let userInputStud = gameInput.askGameType(message:
        OutputView.ProgramDescription.chooseCardStud)
    let stud = gameInput.select(menu: userInputStud) // stud - userInput enum

    guard stud != .quitGame else {
        // 게임 종료
        return
    }
    let inputNumber = gameInput.askNumberOfPlayer(message:
        OutputView.ProgramDescription.askNumberOfPlayer) // numberOfPlayer
    do {
        let controller = try GameController(stud: stud,
                                            numberOfPlayer: inputNumber)
        // 게임 진행
    } catch let error as GameController.InitError {
        // 어떤 exception인지 catch
        switch error {
        case .quit: OutputView().showResult(text:error)
            break
        case .wrongMenu: OutputView().showResult(text:error)
        case .wrongPlayerNumber: OutputView().showResult(text:error)
        }
    } catch {
        print("unknown error")
    }
}

```

<br/>

### 수정사항 \#2 : outputView description
OutputView의 showResult()를 통해 내용을 출력 할때 description(속성 값)이 아니라 객체 자체를 넘길 수 있도록 OutputView의 `showResult(이름: 파라미터)`의 함수 내부를 `파라미터.description`으로 변경
```Swift
func showResult(text: CustomStringConvertible) {
        print(text.description)
    }
```
<br/>


### 수정사항 \#3 : enum 에 rawValue와 Associated Value
enum에 rawValue와 Associated Value를 같이 쓸 수 없을까해서 아래처럼 썼더니 에러 발생.
```swift
enum CardStudMenu: String {
        case one(Int) = "1"
        case two(Int) = "2"
        case quitGame = "q"
        case wrongInput
    }
// Enum with raw type cannot have cases with arguments
```
[스택오버플로우](https://stackoverflow.com/questions/24171814/can-associated-values-and-raw-values-coexist-in-swift-enumeration)에서 되는 방법을 찾았다.
<br/>
하지만 이런식으로 연관값으로 써보려고했는데 내 코드에서의 연관값은 이렇게 활용하는게 아닌거같아서 코드에는 쓰지 않았다. :-(

```swift
enum CardStudMenu {
        case one(Int)
        case two(Int)
        case quitGame
        case wrongInput
    }

extension GameInputView.CardStudMenu: RawRepresentable {
    public typealias RawValue = String

    public var rawValue: RawValue {
        switch self {
        case .one:     return "1"
        case .two:   return "2"
        case .quitGame:
            <#code#>
        case .wrongInput:
            <#code#>
        }
    }
}
```
<br/>

### 수정사항 \#4 : range error
> 남은 카드 수 체크 부분

***카드 부족 시 게임 종료 기준에 플레이어 수 만 입력할 게 아니라 딜러 수도 입력해야함!*** <br/>
Deck에 남아있는 카드 수와 다음 판을 하기위해 필요한 카드 수를 계산할때 `cardDeck.hasEnoughCards(numberOfNeeded: self.studType * self.numberOfPlayer)`처럼 딜러 수를 체크하지 않고 플레이어 수만 체크했더니 만약 카드 9장이 남아있을때 5카드스터드, 플레이어 수 1을 선택하니까 게임이 진행 됐는데, 카드부족 메시지가 뜨면서 종료되는게 아니라 디버그모드가 뜨면서 강제 종료돼버림! <br/>
원인은 5*1 < 9 조건만 체크하고 넘어갔다가, 딜러의 카드스택을 만들려다 보니 4장밖에없어서 카드 수가 부족했던 것!
- 해결 : 남아있는 카드 비교 함수에 넘기는 인자에 딜러 수까지 포함에서 넘김
  - `cardDeck.hasEnoughCards(numberOfNeeded: self.studType * (self.numberOfPlayer+1))`

```
<콘솔 화면>
총 12장의 카드가 남아있습니다.

카드 게임 종류를 선택하세요.
 1. 7카드
 2. 5카드
1
참여할 인원 수를 입력하세요.
1
참가자#1 [♦️8, ♣️A, ♠️2, ♥️K, ♣️Q, ♦️10, ♦️9]
// (lldb) - 디버그모드 실행됨
```
