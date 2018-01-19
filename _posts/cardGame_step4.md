# CardGame step4

### play()에서의 흐름제어
현재는 play()를 실행하기 전 단계에서 if문으로 cardDeck.count()를 GameController의 hasEnoughCard()에 넘겨주면서 판단.
play()에 controller 자체를 넘기는 방법으로 수정? -> play(controller: GameController) 이렇게!

cardDeck.count()를 가져와서 확인하는 것을 줄이는 방법으로, main의 play()함수에 파라미터로 controller자체를 넘겨주고 play() 내부에서 카드스택을 만들기 전에 확인하고 카드 수가 충분하면(hasEnoughCard()에서 true를 리턴받으면) 나머지 게임 로직이 실행되도록 감싸고싶었는데, 이렇게 함수 내부에서 로직을 실행하고 흐름제어를 상위모듈에서 가져오게 하는 방법은 어떻게 해야할지? play()의 리턴타입을 어떻게 해야하는지? throws키워드를 이럴 때 사용해야하는지?!

### outputView description
outputView호출 할때 description이 아니라 객체 자체를 넘길 수 있도록 outputView의 showResult()내부를 `파라미터.description`으로 변경

### init throws 사용
[init?과 init throws중에 어떤걸 써야하는지에 관한 글](https://owensd.io/2015/07/07/init-vs-init-throws/)
- GameController객체 만들면서 객체 자체가 게임 진행여부 판단의 주체?가 될수 있게 하기위해 init?을 사용.

         init?(stud: GameInputView.CardStudMenu,
              numberOfPlayer: Int?) {
            // setting stud
            switch stud {
            case .one:
                self.studType = 7
            case .two:
                self.studType = 5
            case .quitGame:
                return nil // 게임 종료 메시지
            case .wrongInput:
                return nil // 잘못된 값 입력 메시지
            }
            // setting numberOfPlayer
            guard let number = numberOfPlayer else {
                return nil
            }
            if number >= 0 && number <= 4 {
                self.numberOfPlayer = number
            } else {
                return nil
            }

        }

- 이렇게 하면 상위모듈에서 컨트롤러 객체가 만들어졌을때 / nil일때로 단편적으로만 구분해서, nil인데 어떤 case여서 nil인지 구분이 되지 않는다. 그냥 다 똑같은 문구가 출력되면서 게임이 종료되게되는데, exception상황을 더 상세하게 구분짓기위해서 init에 throws키워드를 넣고 에러처리를 해야하는지!?
- 이렇게 되면 main에서 옵셔널 바인딩 하는 과정에서 어떻게 처리해야할지 모르겠다 ㅠ
  main.swift코드
      let unconfirmedController = GameController(stud: stud, numberOfPlayer: inputNumber)

              guard let controller = unconfirmedController else {
                  runProgram = false
                  // 게임 종료 메시지 출력 - 여기서 어떤 케이스여도 어떤 종료조건인지 체크가 되지 않음
                  outputView.showResult(text:
                      OutputView.ProgramDescription.quitGame)
                  break
              } // 게임 진행


### init? 사용
> GameController객체 만들면서 객체 자체가 게임 진행여부 판단의 주체?가 될수 있게 하기위해 init?을 사용.

```Swift
    struct GameController {
        private (set) var studType: Int
        private (set) var numberOfPlayer: Int
        let inputView = GameInputView()

        /*
         상위 모듈에서 GameController객체가 nil이 되면
         게임 진행이 되지 않도록 init? 설정
         */

        init?(stud: GameInputView.CardStudMenu,
              numberOfPlayer: Int?) {
            // setting stud
            switch stud {
            case .one:
                self.studType = 7
            case .two:
                self.studType = 5
            case .quitGame:
                return nil // 게임 종료 메시지
            case .wrongInput:
                return nil // 잘못된 값 입력 메시지
            }

            // setting numberOfPlayer
            guard let number = numberOfPlayer else {
                return nil
            }
            if number >= 0 && number <= 4 {
                self.numberOfPlayer = number
            } else {
                return nil
            }

        }


    // main.swift
      let unconfirmedController = GameController(stud: stud, numberOfPlayer: inputNumber)

            guard let controller = unconfirmedController else {
                runProgram = false
                // 게임 종료 메시지 출력
                outputView.showResult(text:
                    OutputView.ProgramDescription.quitGame)
                break
            } // 게임 진행

```




### enum 에 rawValue와 Associated Value
enum에 rawValue와 Associated Value를 같이 쓸 수 없을까해서
```swift
enum CardStudMenu: String {
        case one(Int) = "1"
        case two(Int) = "2"
        case quitGame = "q"
        case wrongInput
    }
```
이렇게 했더니 이런 에러가 남 : Enum with raw type cannot have cases with arguments
스택오버플로우에서 찾아보니까 된다고 함
https://stackoverflow.com/questions/24171814/can-associated-values-and-raw-values-coexist-in-swift-enumeration

하지만 이런식으로 연관값으로 써보려고했는데 연관값은 이렇게 활용하는게 아닌거같아서 안함 ㅠㅠㅠ
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

### range error
> 남은 카드 수 체크시

카드 부족 시 게임 종료 기준에 플레이어 수 만 입력할 게 아니라 딜러 수도 입력해야함!
딜러 수를 체크하지 않고 플레이어 수만 체크하니까 만약 카드 9장이 남아있을때 5카드스터드, 플레이어 수 1을 선택하니까 게임이 진행 됐는데 카드부족 메시지가 뜨면서 종료되는게 아니라 강제 종료돼버림. 원인은 5*1 < 9 조건만 체크하고 넘어갔다가, 딜러의 카드스택을 만들려다 보니 4장밖에없어서 카드 수가 부족했던 것!
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
