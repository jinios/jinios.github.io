# Step2 - Object 분석

## 프로그래밍 요구사항
- 사용자가 입력하는 메뉴를 객체(Object) 형식도 입력할 수 있도록 개선한다.
- 단순한 JSON 객체(object) 문자열을 입력하면 내부 요소들을 분석해서 사전(Dictionary)으로 저장하도록 구현한다.
- JSON 객체 값(value)에 포함될 수 있는 데이터는 문자열(String), 숫자(Number), 부울 true/false(Bool) 만 가능하다고 가정한다.
- JSON 내부 요소를 사전(Dictionary)에 저장하고 문자열, 숫자, 부울 요소의 개수를 출력한다. 단, JSON 배열 내부에는 객체가 포함될 수 있다고 가정한다.

## 프로그램 구조
[JSON_Step2 코드 바로가기](https://github.com/youth27/swift-jsonparser/tree/jsonparser-step2/JSONParser/JSONParser)
- (최종) InputView - Parser - (JSONData) - ValueCounter - (CountInfo) - OutputView
- (PR 전) InputView - TypeConductor - (String) - MyArray / MyObject -  ParsingTargetFactory - ([String],[String:String]) - JSONDataFactory - ValueCounter - (CountInfo) - OutputView
\* 괄호 안은 리턴되는 타입


## Step2에서의 문제점
>**대대적인 로직 변화.** <br/>
JSON_Step2를 진행에 가장 많은 시간을 보냈고 가장 많은 양의 변화가 있었다. 결과적으로는 프로토콜, 인터페이스 구조에대해서 공부해볼 수 있었던 시간이었지만, 라이브 코드리뷰까지하며(물론 엄청 좋은 경험이었다.) 피드백을 받았어야할 정도로 로직이 복잡했다. <br/>
프로그램 구조가 위와같이 정리되기 전에는 이러한 구조였다. (엄청 복잡함 주의)

- InputView - TypeConductor - (String) - MyArray / MyObject -  ParsingTargetFactory - ([String],[String:String]) - JSONDataFactory - ValueCounter - (CountInfo) - OutputView
  - MyArray, MyObject 객체 : 지금은 제거한 객체이지만 일단 converting이 필요한 문자열을 아예 객체로 만들어버리면 나중에 중첩되는 구조로 갔을때 더 편할 것 같아서, MyArray와 MyObject구조체를 만들었다.
  - ParsingTarget 프로토콜 : 커스텀하게 만든 MyArray,MyObject객체를 한번에 다루기 위해(Parsing하고 DataCoverting하는 로직은 똑같으니까) ParsingTarget이라는 프로토콜을 선언했다.
  - ParsingTargetFactory : 위에서 언급한 객체때문에 Input값의 조건에 따라 분기되고, 그에 맞는 객체를 만드는 팩토리객체까지 필요하게되었다.

## 코드리뷰 Before & After

### 1. protocol 활용
#### Before Code Review
- 변경 전 코드 흐름. : MyArray / MyObject - ParsingTargetFactory 호출, String 전달 - [String] / [String:String]리턴 - MyArray / MyObject - JSONDataFactory호출, [String] / [String:String]전달 - JSONData리턴 -  ValueCounter - OutputView
- MyArray와 MyObject를 묶는 용도로만 ParsingTarget을 만들고 프로토콜에 아무 내용도 없었다.
- JSONDataFactory의 convertValues(ParsingTarget: parsingTarget) : switch-case를 써서 Array인지 Object인지 구분하고있었다.
- MyArray와 MyObject는, 자신이 가지는 String속성값을 넘겨서 결과적으로 JSONData를 리턴하는 역할이었다. 이를 위해 내부에서 String을 자르는 동작과, 잘라진 String을 타입변환하는 동작을 하는 객체를 호출하는 방식으로 짜여져있었는데, 결국 로직을 더 복잡하게 만드는 원인이 됐다.

```swift
// JSONDataFactory.swift
struct JSONDataFactory {

    func convertValues (_ parsingTarget: ParsingTarget) -> JSONData {
        var parsedJSONData : JSONData

        switch parsingTarget {
        case let parsingTarget as MyArray:
           let parsedArray = makeConvertedArray(parsingTarget.makeMyType())
           parsedJSONData = JSONData.ArrayValue(parsedArray)
        case let parsingTarget as MyObject:
            let parsedObject = makeConvertedObject(parsingTarget.makeMyType())
            parsedJSONData = JSONData.ObjectValue(parsedObject)
        default:
          ...
        }
        return parsedJSONData
    }
    ...

// ParsingTarget.swift - 비어있는 프로토콜이었음

protocol ParsingTarget {

}

```
### 개선한 내용
- String타입의 속성을 가진 객체(MyArray, MyObject) 제거. 로직이 복잡해질 뿐 필요 없는 객체.
- convertValues()함수 제거
  - 프로토콜 사용: ConvertTarget 프로토콜에 필요한 기능을 추가하고 분석해야할 대상이 어떤 객체인지 분기문에서 확인 할 필요 없이 바로 로직 실행
  - extension 사용: Array와 Dictionary를 extension하여 ConvertTarget프로토콜로 묶음. 이렇게되면서 Parser에서 바로 규칙에 따라 분석대상(value)대로 자른 Array나 Dictionary형 객체를 넘겨주면 ConvertTarget 내부에서 JSONData로 형변환되는 구조로 변경
  - 프로토콜 내부의 JSONData로 형변환할때 필요한 로직은 DataTypeConverter객체에 위치함.

#### After Code Review
```Swift
// ConvertTarget.swift

protocol ConvertTarget{
    func matchType(_ target: ConvertTarget) -> JSONData
}

extension Array: ConvertTarget {
    func matchType(_ target: ConvertTarget) -> JSONData {
        let dataConverter = DataTypeConverter()
        var parsedJSONArray : [JSONData] = []
        parsedJSONArray = dataConverter.makeConvertedArray(target as! [String])
        // 프로토콜 내부에서 DataTypeConverter 로직에 접근하여 스스로 JSONData타입을 리턴
        return JSONData.ArrayValue(parsedJSONArray)
    }
}

extension Dictionary: ConvertTarget {
    func matchType(_ target: ConvertTarget) -> JSONData {
        let dataConverter = DataTypeConverter()
        var parsedJSONObject : Dictionary<String, JSONData> = [:]
        parsedJSONObject = dataConverter.makeConvertedObject(target as! [String:String])
        return JSONData.ObjectValue(parsedJSONObject)
    }
}

// DataTypeConverter.swift - JSONDataFactory에서 이름변경

struct DataTypeConverter {

    func makeConvertedArray (_ targets: [String]) -> [JSONData] {
        ...
        return parsedJSONData
    }

   func makeConvertedObject (_ targets: Dictionary<String, String>) -> Dictionary<String, JSONData> {
        ...
        return parsedJSONData
    }

//

```
#### main.swift의 변화
변경 전 로직
```swift

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
변경 후 로직
```swift
let inputView = InputView()
let userInput = inputView.askUserInput()

let parser = Parser()
let convertedValues = parser.matchValuesToJSONType(userInput)

let counter = ValueCounter(targetToCount: convertedValues)
let countInfo = counter.makeCountInfo()

let outputView = OutputView()
outputView.showResult(countInfo)
```
main에서 로직 순서에 따라 각 객체를 호출하던 전과 달리, 프로토콜을 적용하여 변경한 후에는 **InputView 이후 Parser만 호출하면 내부에서 타입에 따라 로직이 실행되고 JSONData(convertedValue)를 리턴** 하는 구조로 변경됨

***
#### 배운 점
- 단순화된 인터페이스 구조
  - main에 있는 호스트 코드가 복잡해지기보다는 추상화 단계를 거쳐 단순화하는게 더 좋다.
  - 인터페이스 동작 구조 : 메소드를 호출하고 또 다른 메소드를 호출하는 동작 흐름
  - 어떤 메소드를 호출하고 나서 이어서 꼭 다른 메소드를 호출해야만 한다면 메소드끼리도 의존성이 높아짐
  - **switch-case로 메소드 내부에서 무언가를 비교**하고, 대상은 다르지만 **동작을 똑같이 하는 메소드**가 있다면 **프로토콜/제네릭 등을 활용해서 추상화**하려는 시도를 해야함.

### 2. 주석 예약어 사용
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

### <라이브 코드리뷰 이후 수정한 사항들>
큰 수정사항은 아래와 같다.
1. ConvertTarget수정 - 빈 프로토콜로 묶기만 하기보다는 Collection으로 리턴하거나 차라리 ConvertTarget에 더 기능을 구현할 것

  - 수정된 코드 : ConvertTarget.swift
  ``` Swift
  // 중복되는 JSONData를 처리하기 위해 Arry와 Dictionary를 확장하여 프로토콜 구현
  // Parser에서 체크된 target을 JSONData로 형변환하는 작업

  protocol ConvertTarget{
      func matchType(_ target: ConvertTarget) -> JSONData
  }

  extension Array: ConvertTarget {
      func matchType(_ target: ConvertTarget) -> JSONData {
          let dataConverter = DataTypeConverter()
          var parsedJSONArray : [JSONData] = []
          parsedJSONArray = dataConverter.makeConvertedArray(target as! [String])
          return JSONData.ArrayValue(parsedJSONArray)
      }
  }

  extension Dictionary: ConvertTarget {
      func matchType(_ target: ConvertTarget) -> JSONData {
          let dataConverter = DataTypeConverter()
          var parsedJSONObject : Dictionary<String, JSONData> = [:]
          parsedJSONObject = dataConverter.makeConvertedObject(target as! [String:String])
          return JSONData.ObjectValue(parsedJSONObject)
      }
  }
  ```
2. 여러 객체를 main에서 조합하지 말고 ParseTargetFactory를 JSONDataFactory안에서 조합해보기.
  - 수정된 코드 : JSONDataFactory.swift (-> DataTypeConverter.swift로 파일명 변경)
  ```Swift

  struct JSONDataFactory {
    func makeConvertedData (_ targets: ConvertTarget) -> JSONData {
        var parsedJSONArray : [JSONData] = []
        var parsedJSONObject : Dictionary<String, JSONData> = [:]

        switch targets {
        case let targets as Array<String>:
            parsedJSONArray = makeConvertedArray(targets)
            return JSONData.ArrayValue(parsedJSONArray)

        case let targets as Dictionary<String,String>:
            parsedJSONObject = makeConvertedObject(targets)
            return JSONData.ObjectValue(parsedJSONObject)

        default:
            return JSONData.ArrayValue(parsedJSONArray)
        }
    }
  ...
  ```
  JSONDataFactory 객체 안에 있던 makeConvertedData()메소드는 ConvertTarget 프로토콜을 구현하면서 제거되었다. 해당 메소드가 없어지면서 객체의 책임은 *변환 가능한 String을  JSONData로 변환해주는 역할* 로 압축되었기 때문에 이름을 변경했다. (DataTypeConverter)<br/>
  또한 프로토콜 구현으로 MyArray와 MyObject와 같은 필요없어진 구조체를 제거했다. <br/>
  **이 변화로 이 글의 상단에 기재된 프로그램 흐름이 완성된다.** (String) - Parser - (변환 가능한 String) - DataTypeConverter - (JSONData) - ValueCounter ... <br/>
  (extension과 protocol의 적절한 사용으로 switch-case를 지닌 긴 메소드가 없어지고 객체가 더 간단해 지는 것을 예시로 들기 위해 제거된 코드 부분 기재함)
3. ParseTargetFactory을 static 메소드로 수정 - String을 공통적으로 쓰는 static 메소드들이 많으니 차라리 string을 인스턴스변수로 선언하고 ParseTargetFactory안의 메소드가 필요할 때마다 인스턴스를 만들고 사용 할 것.
  - 수정 내용 : Parser로 이름변경. 다만 static으로 수정하지 않고 필요한 메소드를 호출할 때 마다 필요한 인자를 넘기는 방식으로 수정함. <br/> (모든 메소드가 String으로 작업하는 내용이었지만 인스턴스 변수로 선언하고 공통적인 같은 값을 사용하는 구조가 아니었음.)
  - makeTargetArrayWithObject이렇게따로 나누지 말고 makeArray안에서 돌면서 makeobject메소드를 부르는 게 더 나아보임
    - 수정 내용 : 수정하지 않았다. 입력값이 object만 있을때랑 array 안에 object가 있을때랑 분리하는 로직이 다름. 배열 안에 객체가 있는 경우를 판단하는 조건문을 makeTargetArray()로 옮길 수 는 있음. <br/> ParseTargetFactory 안에서 메소드들이 조합해서 쓰이는데, 이때 각각 메소드들이 인자로 받는 String이 객체가 초기화될때 사용되는 공통적인 String이 아니기때문에 (메소드마다 String을 쓰긴 하지만 다 같은 문자열을 받아 쓰는게 아님) static으로 두고 메소드를 호출할때마다 인자를 넘겨주면서 사용하고, 대신에 decideInputType() 메소드를 static으로 변경하기.
