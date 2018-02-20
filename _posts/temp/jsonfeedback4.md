# Step4 - 중첩(Nested) 구조 분석

### 프로그래밍 요구사항
- JSON 객체 내에 배열이나 객체가 한단계 포함(Nested)된 경우를 지원하도록 개선한다.
- JSON 배열 내에 배열이 한단계 포함(Nested)된 경우도 지원하도록 개선한다.
- 콘솔에 출력되는 문자열 수정 : `n개의 데이터 중에 문자열 n개, 숫자 n개...`로 표시되던걸
`n개의 배열/객체 데이터 중에 문자열 n개, 숫자 n개...`로 표시되도록 수정.

### 구현 / 수정사항
- 중첩배열 로직 분리
- CountInfo의 책임

### \#1. 중첩배열 로직 분리
#### 로직 설계 고민
- matchType에서 Int형변환하는 곳에서 자꾸 string이 들어가서 걸려서 버그 뜨는 문제. 왜string인데 value.contains("[")로 가지 않았을까?
  - 해결: 그래서 가장 외부 괄호를 제거하면서 하니까 에러 없이 잘 처리됐다. 하지만 GrammarChecker의 execute에서 들어오는 input마다 모두 괄호를 잘라버리면 중첩된 배열일때도 괄호를 없애고 parsing을 해서 배열하나로 카운트되는게 아니라, 배열 안의 value들이 다 파싱되는 문제가 있었음
    - 배열이 value로 들어있을때는 외부의 괄호가 제거되고 execute가 실행되지 않도록, 가장 외부의 괄호가 제거되는 로직은 프로그램 맨 처음 시작할때만 하고, 중간에 parsing될때는 괄호가 trimming되지 않도록 trimming하는 로직을 분리!
    - 배열만 잇을댄 처음부터 괄호제거(그래야 안의 밸류가 나옴)
    - 배열안에 또 배열이 잇을때(matchvalue에서 알수있음- 여기서만 이렇게 메소드 실행시키면된다는소리..) : 배열 안의 배열이있으면 괄호제거를 하면안됨.
    - input입력 > `String`분리 > execute에 `String`넘기기 > 하다가 배열/객체 나오면 > execute에 또 바로 `String`넘기기
  - GrammarChecker의 메소드 재사용이 가능할지 고민
    - MyArray나 MyObject처럼 프로토콜로 다시 구현해야할지
    - **extractArrayValue 재사용하는 방식으로 바꿈(정규식패턴을 인자로 넘김)**
```Swift

    let arrayPattern = "true|false|\".+?\"|(\\d+)|(\\[[^\\[\\]]*\\])|(\\{[^\\{\\}]*\\})"
    let objectPattern = "(\".+?\")\\:(true|false|\\\".+?\\\"|\\d+|\\[.+?\\])"
    let arrayValuePattern = "true|false|(\\d+)|\".+?\"|(\\[[^\\[\\]]*\\])|(\\{[^\\{\\}]*\\})"
    let objectValuePattern = "(\".*\"):((true|false)|(\\{.*\\})|(\\d+)|(\".*\")|(\\[.*\\]))"
    let nestedArrayPattern = "true|false|\".+?\"|(\\d+)"

  // 중첩된 배열과 아닌 배열을 분석하는데 필요한 패턴을 따로 두고,
  // extractArrayValue()메소드를 재사용하는 방식으로 변경

    func forNestedArray (_ input: String) throws -> ([String],Parser.ParseTarget) {
        if isValidNestedArray(input) {
            return (extractArrayValue(input: input, regexPattern: nestedArrayPattern),Parser.ParseTarget.list)
        } else {
            throw GrammarChecker.FormatError.invalidArray
        }
    }

    private func isValidNestedArray (_ input: String) -> Bool {
        let formatMatchValues = extractArrayValue(input: input, regexPattern: nestedArrayPattern)
        for value in formatMatchValues {
            return isValidValueInArray(value)
        }
        return false
    }
```


### \#2.CountInfo의 책임
콘솔에 출력하는 결과 메시지에, 최초로 입력된 JSON데이터의 종류를 함께 출력해야해서 분석한 데이터 개수 뿐만아니라 JSON데이터 종류까지 알아야하는 문제가 있었다.

#### 이슈
- 최초로 입력된 JSONData의 종류는 Parser만 알고 있다.
- 데이터 분석과 그 종류를 카운팅하는 객체가 분리되어있고, 특히 OutputView는 CountInfo라는 데이터오브젝트에만 접근해서 출력 결과물을 넘겨받고있는 상태다.

#### Before Code Review
OutputView는 로직-DTO 흐름 상 CountInfo에만 접근할 수 있다고 생각해서, CountInfo에 새로운 속성을 추가해서 최초 데이터 타입을 DTO에 함께 넣을 수 있도록 만들었다.

```swift
// CountInfo.swift

struct CountInfo {
    private (set) var countOfInt : Int
    private (set) var countOfBool : Int
    private (set) var countOfString : Int
    private (set) var countOfObject : Int
    private (set) var countOfArray : Int
    private (set) var countOfJSONData : Int
    private (set) var parseType : Parser.ParseTarget // 속성 추가
}

//OutputView.swift

struct OutputView {
  func showResult(_ countInfo: CountInfo) {
       print("\(countInfo.countOfJSONData)개 \(selectType(countInfo)) 중에 \(makeResultMessage(countInfo))가 포함되어 있습니다.")
   } // 추가된 CountInfo속성에 접근해서 결과 출력
}
```
- JK's Comment : CountInfo 객체의 역할과 책임, 응집도 측면에서 parseType 을 추가한게 좋은 선택일까요? 단지 OutputView에서 출력 문구 필요해서 앞에 영향을 준 것 같습니다. parse하고 결과적으로 parseType를 매개변수로 그냥 넘길수는 없나요? <br/>OutputView가 parseType에 의존해야 한다면 CountInfo 대신 다른 방법으로 접근할 수 있지 않을까 다시 한 번만 고민해보세요.
  - JK의 이 질문에 생각해보다가, 계속 `로직-데이터-로직-데이터`의 흐름을 깨면 안될 것 같다고 생각해서 여기서도 또 ParseType을 넘겨주는 DTO를 만들어야되지 않겠냐고 생각했었다. <br/>하지만 **효율성 측면에서** Parser, GrammarChecker라는(비교적 로직의 초반에 존재하는) 객체가 알고있는 정보를 후반부 로직에 옮겨다니게 하는 것 보다는, **Datatype정보를 알고 있는 객체가 그 정보가 필요한 곳에 바로 넘기는 것이 더 좋다** 는게 결론이었다. 더군다나 하나의 속성을 위해서 새로운 데이터객체를 만들어서 넘기는 게 더 비효율적일것이다. <U>의존성을 최대한 줄이기 위해 DTO를 만들어서 쓰지만, 경우에 따라 비교적 더 효율적인 방법을 쓰는게 정답이라는 생각이 들었다.</U>

#### After Code Review
```Swift
// GrammarChecker.swift

  func execute (_ userInput: String?) throws -> ([String],Parser.ParseTarget) {
        let input = userInput ?? ""

        if input.hasPrefix("[") && input.hasSuffix("]") {
            if isValidArray(input) {
               return (extractArrayValue(removeBrackets(input)),Parser.ParseTarget.list)
            } else {
                throw GrammarChecker.FormatError.invalidArray
            }
        }
        if input.hasPrefix("{") && input.hasSuffix("}") {
            if isValidObject(input) {
               return (extractObjectValue(input),Parser.ParseTarget.object)
            } else {
                throw GrammarChecker.FormatError.invalidObject
            }
        }
        throw GrammarChecker.FormatError.invalidInput
    }

// Parser.Swift

  enum ParseTarget {
      case list
      case object
  }

  static func matchValues(_ target: (parseValue: [String], parseType: ParseTarget)) throws -> JSONData {
      let convertTarget = target.parseValue
      let targetType = target.parseType

      if targetType == Parser.ParseTarget.list {
          do {
              let JSONArray = try convertTarget.matchType(convertTarget)
              return JSONArray
          } catch let error {
              throw error
          }
      } else if targetType == Parser.ParseTarget.object {
          let targetObject = newTargetObject(convertTarget)
          do {
              let JSONObject = try targetObject.matchType(targetObject)
              return JSONObject
          } catch let error {
              throw error
          }
      }
     return try [""].matchType([""])
  }

// OutputView.Swift

// GrammarChecker와 Parser에서 OutputView의 showResult()로 인자를 넘겨줌
func showResult(_ countInfo: CountInfo, _ parseType: Parser.ParseTarget) {
        print("\(countInfo.countOfJSONData)개 \(selectType(parseType)) 중에 \(makeResultMessage(countInfo))가 포함되어 있습니다.")
    }

```
- 로직초반의 GrammarChecker와 Parser객체에서 데이터를 분석하면서 최초 JSON형태(enum으로 저장함)를 OutputView로 넘겨주고, OutputView의 showResult()가 인자로 그 값을 받아서 출력에 활용.
