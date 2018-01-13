# Step2 - Object 분석
## 프로그래밍 요구사항
- 사용자가 입력하는 메뉴를 객체(Object) 형식도 입력할 수 있도록 개선한다.
- 단순한 JSON 객체(object) 문자열을 입력하면 내부 요소들을 분석해서 사전(Dictionary)으로 저장하도록 구현한다.
- JSON 객체 값(value)에 포함될 수 있는 데이터는 문자열(String), 숫자(Number), 부울 true/false(Bool) 만 가능하다고 가정한다.
- JSON 내부 요소를 사전(Dictionary)에 저장하고 문자열, 숫자, 부울 요소의 개수를 출력한다. 단, JSON 배열 내부에는 객체가 포함될 수 있다고 가정한다.

## 프로그램 구조
[JSON_Step2 코드 바로가기](https://github.com/youth27/swift-jsonparser/tree/jsonparser-step2/JSONParser/JSONParser)
InputView - Parser - (JSONData) - ValueCounter - (CountInfo) - OutputView <br/>
\* 괄호 안은 리턴되는 타입


## Step2 진행의 문제점!
JSON_Step2를 진행에 가장 많은 시간을 보냈고 가장 많은 양의 변화가 있었다. 결과적으로는 프로그램의 큰 흐름이 위에 작성한대로 수정되었지만, 라이브 코드리뷰까지하며(...) 피드백을 받았어야할 정도로 로직이 복잡했다. <br/>
프로그램 구조가 위와같이 정리되기 전에는 이러한 구조였다. (엄청복잡)
- InputView - TypeConductor - (String) - MyArray / MyObject -  ParsingTargetFactory - ([String],[String:String]) - JSONDataFactory - ValueCounter - (CountInfo) - OutputView
  - MyArray, MyObject 객체 : 지금은 제거한 객체이지만 일단 converting이 필요한 문자열을 아예 객체로 만들어버리면 나중에 중첩되는 구조로 갔을때 더 편할 것 같아서, MyArray와 MyObject구조체를 만들었다.
  - ParsingTarget 프로토콜 : 커스텀하게 만든 MyArray,MyObject객체를 한번에 다루기 위해(Parsing하고 DataCoverting하는 로직은 똑같으니까) ParsingTarget이라는 프로토콜을 선언했다.
  - ParsingTargetFactory : 위에서 언급한 객체때문에 Input값의 조건에 따라 분기되고, 그에 맞는 객체를 만드는 팩토리객체까지 필요하게되었다.

## 코드리뷰 Before & After

### extension의 사용
#### Before Code Review
extension을 코드가 길어질때 가독성을 높이는 의미로 사용했었음. 예시 코드의 ParsingTargetFactory에서는 Array를 만들때와 Object를 만들때 필요한 메소드들을 분리하기위해 사용.

```Swift
struct ParsingTargetFactory {

    static func setTargetToArray (_ input: String) -> [String] {
        // ... 생략
    }
      // ...
}

extension ParsingTargetFactory {

    static func makeTargetArray (_ changeTarget: String) -> [String] {
        // ... 생략
    }
```
- Reviewer's Comment
  - 하나의 구현체 내부에서 의미있는 단위 코드가 길어져서 영역을 구분하기 위해 extension을 사용하는것은 불필요.
  - 이런경우라면 extension을 쓰기보다는 주석을 붙이거나 `//MARK: //TODO: //FIXME:` 같은 방식이 좋을 것 같습니다.
  - extension은 말한것처럼 좀더 역할이 뚜렷하게 구분되는 경우에 나누거나 public / private 메소드들을 구분하는 경우 사용을 추천. private extension은 모두 private으로 감춰지며, 결과적으로 private 메소드들만 들어갈 수 있음.

#### After Code Review
- 주석 예약어 사용
  - `// MARK:`주석을 사용함. `// MARK:`는 **주석 예약어** 인데, Swift가 나오기 전 Objective-C에서는 `#pragma mark`라는걸 이용해서 긴 코드 내에서 코드영역을 구분해서 사용할 수 있었다고 한다. Swift에서 사용이 불가해지면서 주석 예약어라는게 나왔는데, `//MARK: //TODO: //FIXME`가 있다.
- `// MARK:` 코드 내용을 분리하고 코멘트를 달 때 (북마크 같은 것)
- `// TODO:` 앞으로 해야 할 작업
- `// FIXME:` 고쳐야 할 부분
- 주석 예약어를 쓰면 Xcode의 Navigation dropdown서 주석 예약어대로 분리된 트리를 볼 수 있다. [참고했던 사이트](https://littlebitesofcocoa.com/207-annotating-swift-with-marks-todo-s-and-fixme-s)

```Swift
static func matchValues(_ target: (parseValue: [String], parseType: ParseTarget)) throws -> JSONData {
    ...
       return try [""].matchType([""])
    }

    // MARK: Private functions to make object

    static func newTargetObject(_ pairsOfValue: [String]) -> [String:String] {
      ...
        return stringDictionaryToObject
    }
```


### protocol 활용
#### Before Code Review
- convertValues() : if를 써서 Array인지 Object인지 구분하고있었다.
- MyArray와 MyObject를 묶는 용도로 ParsingTarget을 만들고 프로토콜에 아무 내용도 없었다...


```Swift
let inputView = InputView()
let userInput = inputView.askUserInput()

let conductor = TypeConductor(userInput)
let parsingTarget = conductor.decideInputType()

let targetFactory = JSONDataFactory()
let convertedValue = targetFactory.convertValues(parsingTarget)
let counter = ValueCounter(targetToCount: convertedValue)
let countInfo = counter.makeCountInfo()

let outputView = OutputView()
outputView.showResult(countInfo)
```
*Reviewer's Comment가 길고 나도 같의 코멘트 한 부분도 있어 같이 씀. (이것만 봐도 당시 로직이 얼마나 잘못됐었는지 알 수 있었다...)*
***
- JK : TypeConductor 와 JSONDataFactory 객체 관계가 불명확한 것 같습니다.
JSONDataFactory가 할 일을 나눈것처럼 보이는데 어떤 기준으로 나눈건가요? 사용방법이 너무 복잡해지고 있습니다.

- 나 : TypeConductor가 JSONDataFactory가 할 일을 나눴다고 보기는 어려운것 같습니다.
원래는 ParsingTargetFactory가 하는 일을 분리한것이고, 제일 외부에 있는 괄호에 따라 *(Array인지 Object인지)* 생성되는 객체가 달라지도록 설계하려다보니, 사용자가 입력한 값을 판단하는 작업을 main에서 할 수는 없어서 Conductor객체가 필요하게 되었습니다.<br/>
TypeConductor는 사용자가 입력한 문자열을 받아서 제일 외부에 있는 괄호를 판단해서 Array인지 Object인지 판단하는 객체입니다.<br/>
ParsingTargetFactory가 각 객체의 기준에 따라서 형변환이 필요한 value들만 분리하는 작업을 하는것이고, JSONDataFactory는 그 이후에 분리된 value들을 가지고 JSONDataType으로 변환해서 입력한 객체형태(Array, Object)로 데이터를 저장하는 작업을 합니다.<br/>
*(위에서 언급된 value들은 형변환이 필요한 String들을 [String]로 담은 것이며, ParsingTargetFactory에서 [String]을 만들기위한 분리 작업과 append작업을 한다.)*

- JK : 그렇군요. 이해됐습니다. TypeConductor 객체는 단지 메소드가 하나만 있고 역할이 너무 단순해서 ParsingTargetFactory 메소드 하나만 떨어져 나간 것 같아 보입니다. 굳이 괄호에 따라 구분된 데이터 구조를 전달하기 보다는 내부 메소드로 만들어서 처리하는게 편리하지 않았을까 싶었습니다. **main에 있는 호스트 코드가 복잡해지기보다 더 단순화하는 인터페이스 구조가 좋을 것 같습니다.** 어떻게 생각하세요?

- 나 : 저도 흐름 상 ParsingTargetFactory에 conductor 기능을 하는 메소드가 있는게 맞다고 생각합니다. 객체 이름도 그렇고요!
그런데 처음에 이렇게 나눈 이유가 ParsingTargetFactory 코드가 너무 긴 것 아닌가 해서 살펴보니 value를 나누는 일 말고 다른 성격의 일을 하는게 타입을 결정하는 일이었습니다. 이 문제때문에 Conductor를 따로 분리했습니다. 이 정도는 한 객체가 가지는 일이 맞는건가요? *(객체는 되도록 한 가지 일만 해야한다는 원칙에 따라 분리했는데, 기능을 또 합쳐도 되는건지 확신이 없었다.)*
그리고 인터페이스구조는 어떤뜻인가요?

- JK : 인터페이스라고 하면 메소드들을 의미한다고 생각하면 되고, 인터페이스 동작 구조는 우선 어떤 메소드를 호출하고 다음 어떤 메소드를 호출해야 하는지 동작 흐름을 의미합니다. **어떤 메소드를 호출하고 나서 이어서 꼭 다른 메소드를 호출해야만 한다면 메소드끼리도 의존성이 높아집니다.** 그 관계를 단순화할 수 있는게 더 좋은 구조입니다. <br/> *(지금은 **"프로토콜로 대상을 묶어서 그 프로토콜에만 의존하여 동작하도록 수정하고, main도 객체도 단순화 하라는 코멘트!"** 라고 이해하지만, 당시에는 어떻게 해야할 지 몰라 좀 헤맸다.)*


### After Code Review
TypeConductor를 제거. 책임을 ParseTargetFactory로 넘김. (decideInputType()함수)<br/> Factory 의미대로 ParseTargetFactory에서 ParseTarget메소드들을 생성하도록 수정.<br/>
ParseTarget 프로토콜에 추상화 할 수 있는 함수들을 추가.<br/>
JSONDataFactory : 위에 지적해주신 switch-case구문을 제거하기 위해 Array와 Dictionary를 ConvertTarget 프로토콜로 추상화하고, convertValues() 함수를 제거했습니다.<br/>
-> converValues() 대신에, ConvertTarget을 makeConvertedData()를 선언하여 convertedArray나 convertedObject를 호출하는 메소드를 구현했습니다.<br/>
-> makeConvertedData()함수 내에서만 호출하도록 변경된 함수들 또한 접근제어자를 private으로 변경했습니다.<br/>
