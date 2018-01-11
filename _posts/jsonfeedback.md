####[JSON_Step1_Code](https://github.com/youth27/swift-jsonparser/tree/jsonparser-step1/JSONParser/JSONParser)

### 프로그래밍 요구사항
>- JSON 데이터 문자열을 입력하는 InputView 구조체를 추가하고, 아래 실행 결과처럼 문자열을 입력받는다.
>- 단순한 JSON 배열(array) 문자열을 입력하면 내부 요소들을 분석해서 배열로 저장하도록 구현한다.
>- JSON 배열에 포함될 수 있는 데이터는 문자열(String), 숫자(Number), 부울 true/false(Bool) 만 가능하다고 가정한다.
>- JSON 내부 요소를 배열에 저장하고 문자열, 숫자, 부울 요소의 개수를 출력한다.

### 프로그램 구조
InputView - ValueProducer - ValueCounter - CountingInfo - OutputView

### InputView
- 사용자의 입력을 받는 구조체

### ValueProducer
- InputView구조체에서 넘어온 값을 JSON요소로 분석 가능하도록, 일정한 기준대로 `[String]`을 만들어서 리턴
- trimmingCharacter사용하여 앞,뒤 괄호 잘라내고 ","로 분리하여 `String`으로 형변환

### ValueCounter
- 이전 객체에서 넘어온 `[String]`의 value를 하나씩 빼서 형변환을 해보고 성공하면 `[AnyObject]`에 값을 담는다.
- 값을 담으면서 value의 형태에 따라 counting도 함께 하고, 각 value (Int,String,Bool)의 갯수를 담은 DTO인 CountingInfo를 리턴한다.
> 이 ValueCounter는 이름으로만 봤을때는 '어떠한 것의 갯수를 세는 객체이구나'라고 그 역할을 유추할 수 있다. 하지만 형변환을 하면서 `[AnyObject]`를 만드는 동시에 value의 수까지 세는 동작을 한꺼번에 하고 있었다. <br/>
> 심지어 결과적으로 CountingInfo만 리턴하지 `[AnyObject]`는 리턴하지 않고 있다는 문제점이 있었다.

### CountingInfo
- ValueCounter에서 생성된 DTO
- 자신의 속성(상태)를 나타내는 결과문장을 만드는 기능도 한다. (makeResultMessage())
  - JSON 이후의 미션을 하다가 받은 피드백인데, 객체의 상태를 나타내주는 문장을 출력하는 기능이 필요하면, **해당 객체가 문장을 만드는 기능을 가지고있고, 만든 문장(String)만 출력하는 객체에 넘기는 방식이 더 좋은것 같다.** (여기서는 그렇게 했는데 JSON이후의 미션에서는 그렇게 안하고 출력객체에서 String만들었다가 지적받았는데, 왜 이렇게 했던걸 잊고있었던것인지 알수없다. 이번기회에 피드백하면서 이젠 헷갈리지 않을 것 같다!)
