# Step3 - 규칙 검사하기
## 프로그래밍 요구사항
- 사용자가 입력한 JSON 데이터 문자열 문법 검사를 담당하는 GrammarChecker 구조체를 추가한다.
- 스위프트 파운데이션에 포함된 정규 표현식 담당 클래스를 활용한다.
- 예외처리를 추가한다.
## 프로그램 구조
[JSON step3 코드 바로가기](https://github.com/youth27/swift-jsonparser/tree/jsonparser-step3)
- InputView - GrammarChecker - Parser - (JSONData) - ValueCounter - (CountInfo) - OutputView

## Step3에서의 이슈
- 정규표현식. 스위프트 정규표현식 클래스.
- enum에서의 description사용
- 에러처리 / 흐름제어

## 코드리뷰 Before & After

### 1. 정규표현식(Regex)
처음에 정규식을 쓸때는 정규식을 분석하고자 할 문자열에 적용만 하면 regex101.com 같은 사이트에서 보여지는대로 (손쉽게!) 분리되는 줄 알았다. 하지만 실상은 생각했던 것 보다 더 복잡했다. (정규식을 문자열에 어떻게 적용시켜야하는지는 고민하지 않았던...지난 날의 나를 ~~꾸짖~~) <br/> 사용해보면서 생각보다  정규표현식도 중요하지만, 그만큼 '내가 이 정규식으로 어떤 메소드를 선택해서 사용할 것인지'와 '그 메소드가 리턴하는 형태가 무엇인지'를 고려해서 적절하게 정규식과 메소드를 조화시키는게 중요한 것 같다.



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
에러가 발생할 모든 곳에서 throw, do-try-catch를 하는 것 보다, 객체 내부에서 bool값으로 흐름제어를하고 상위모듈에서만 에러 처리, 혹은 상위모듈까지 에러를 끌고 오지 않는 방법이 중요! (사실 do-catch는 프로그램을 강제종료해야하는 피치못할 상황에서만 사용하는 정도임)
