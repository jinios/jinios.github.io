# Step3 - 규칙 검사하기
## 프로그래밍 요구사항
- 사용자가 입력한 JSON 데이터 문자열 문법 검사를 담당하는 GrammarChecker 구조체를 추가한다.
- 스위프트 파운데이션에 포함된 정규 표현식 담당 클래스를 활용한다.
- 예외처리를 추가한다.
## 프로그램 구조
[JSON step3 코드 바로가기](https://github.com/youth27/swift-jsonparser/tree/jsonparser-step3)
- InputView - GrammarChecker - Parser - (JSONData) - ValueCounter - (CountInfo) - OutputView

## Step3에서의 이슈
- 정규표현식 / 스위프트 정규표현식 클래스
- enum에서의 description사용
- 에러처리 / 흐름제어

## 코드리뷰 Before & After

### 1. 정규표현식(Regex)
처음에 정규식을 쓸때는 정규식을 분석하고자 할 문자열에 적용만 하면 regex101.com 같은 사이트에서 보여지는대로 (손쉽게!) 분리되는 줄 알았다. 하지만 실상은 생각했던 것 보다 더 복잡했다. (정규식을 문자열에 어떻게 적용시켜야하는지는 고민하지 않았던...지난 날의 나를 ~~꾸짖~~) <br/> 사용해보면서 정규표현식도 중요하지만, 그만큼 '내가 이 정규식으로 어떤 메소드를 선택해서 사용할 것인지'와 '그 메소드가 리턴하는 형태가 무엇인지'를 고려해서 적절하게 정규식과 메소드를 조화시키는게 중요하다고 생각했다.

- 정규식 활용 `matches`함수 : 정규식을선언한 이후에 그 클래스에서 사용하는 방법을 몰랐었는데, 검색으로 유추해서 패턴과 일치한 문자열들을 [String]으로 리턴해주는 matches함수가 있었다. matches에서 리턴한 값을 value마다 하나씩 검사해서 맞는 형식인지 아닌지 검사, 에러처리하는 방식으로 해결했다.
- 정규식 선언 이후에 Parser가 간단해졌다.

```Swift

/*
 중괄호 한 세트 안의 문자 : \{(.*?)\}
 따옴표 한 세트 안의 문자 : \"(.+?)\"

 Array : true|false|\".+?\"|\d+|\{.+?\}
 Object : (\".+?\")\:(true|false|\".+?\"|\d+|\[.+?\])
 이 오브젝트에서 1 depth 정도 더 들어가서 ex) object의 요소를ㅡ"dee":234 이렇게 떼어놨으면, 여기서 "ㅇㅇㅇ":ㅁㅁㅁ 인지 검사를 해야하는 것. 정규식 한 줄로 오브젝트 형태 체크랑, 그 안의 각각 요소가 "key":value 형태로 잘 들어왔는지 체크하기가 힘드니까. 차라리 전체에서 잘라진 작은 덩어리를 검사하는 다른 정규식을 하나 더 만들든가, 로직으로 처리하든가 해서 딕셔너리 형태가 맞는지 검사하는게 더 편한 방법일듯.

 [true,"sdfe",false,123,{"dee":234,"ss":false,"er":"wert"},"rr"]
 {"dee":234,"ss":false,"er":"wert"}
 {"dee":234,"ss":false,"er":["hana","hayul","haun"]}
 ["dee":234,"ss":false]
{"d":"e","t":12,"rr":false,"array":[12,true]}


 GrammarChecker에서
 1. 전체 format 검사하고 내부 값 String배열로 만들기
 2. String배열의 각 value를 하나씩 검사하는 것 만들기 (Array형태일때 딕셔너리가 들어오면 안되는것")
*/

    func matches(for regex: String, in text: String) -> [String] {

        do {
            let regex = try NSRegularExpression(pattern: regex)
            let nsString = text as NSString
            let results = regex.matches(in: text, range: NSRange(location: 0, length: nsString.length))
            return results.map { nsString.substring(with: $0.range)}
        } catch let error {
            print("invalid regex: \(error.localizedDescription)")
            return []
        }
    }
    let testString = "[true,false,123,\"esdf\",{\"dee\":234,\"ss\":false,\"er\":\"wert\"},\"rr\"]"
    let arrayformat = "true|false|[0-9]+|\\{.+?\\}"
    let result = matches(for: arrayformat, in: testString)
    print(result)


```


### 2. enum - description
#### Before Code Review
일반적으로 에러 메시지를 담은 enum을 사용할때는 각 case에 rawValue를 직접 입력했다. 하지만 이런 방식은 enum을 나중에 재사용할 경우 고치기 힘들어진다고 한다.  
(보통 프로그램에서 쓰이는 메시지를 저장한 파일을 따로 관리하고 나중에는 enum과 그 파일을 매핑하듯이 사용하기 때문! \_localizedDescription처럼)
```Swift
enum GrammarError: String, Error {
        case array = "지원하지 않는 배열 형식입니다."
        case object = "지원하지 않는 객체 형식입니다."
    }
```
#### After Code Review
```Swift
enum GrammarError: Error {
        case array
        case object

        var description: String {
            switch self {
            case .array:
                return "지원하지 않는 배열 형식입니다."
            case .object:
                return "지원하지 않는 객체 형식입니다."
            }
        }
    }
```
### 3. Error handling
- 에러처리를 위해서는 `catch`문 내에서 흐름 제어가 반드시 되어야 함. print(에러메시지)만 하는 정도는 흐름제어가 전혀 되지 않는다.
- 그리고, 에러가 발생할 모든 곳에서 `throw`, `do-try-catch`를 하는 것 보다, **객체 내부에서 bool값으로 흐름제어를하고 상위모듈에서만 에러 처리, 혹은 상위모듈까지 에러를 끌고 오지 않는 방법이 중요** 하다는 피드백을 받았다. (사실 do-catch는 프로그램을 강제종료해야하는 피치못할 상황에서만 사용하는 정도로 생각하고 접근해야 한다고 함)

#### Before Code
```Swift
// Parser.swift
// 거의 프로그램 흐름 중 시작부분에 해당하는 Parser의 배열을 만드는 한 메소드에서 에러메시지를 print하고 넘어감
// 흐름제어가 되지 않고, 이렇게 되면 상위모듈까지 에러를 끌고 올라가게 됨.

func setTargetToArray (_ input: String) -> JSONData {
       let grammarChecker = GrammarChecker()
       var targetArray = [String]()
       do {
           targetArray = try grammarChecker.checkArray(input)
       } catch {
           print(GrammarChecker.GrammarError.array.message)
       }
       let JSONArray = targetArray.matchType(targetArray)
       return JSONArray
   }

```

#### After Code
```Swift
// GrammarChecker.swift
// 내부에서 Bool값으로 흐름 제어를 할 수 있는 메서드 execute()

  func execute (_ userInput: String?) throws -> ([String],String) {
        let input = userInput ?? ""

        if input.hasPrefix("[") && input.hasSuffix("]") {
            if checkArray(input) {
               return (checkArrayFormat(input),input)
            } else {
                throw GrammarChecker.FormatError.invalidArray
            }
        }
        if input.hasPrefix("{") && input.hasSuffix("}") {
            if checkObject(input) {
               return (checkObjectFormat(input),input)
            } else {
                throw GrammarChecker.FormatError.invalidObject
            }
        }
        return ([],"")
    }

    // 배열 value체크 후 execute()로 Bool리턴
        func checkArray (_ input: String) -> Bool {
            let formatMatchValues = checkArrayFormat(input)
            for value in formatMatchValues {
                if checkArrayValue(value) {
                    return true
                } else {
                    return false
                }
            }
            return false
        }
```
