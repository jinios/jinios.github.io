# Step6 - 파일 입출력 구현

### 구현사항
- Command Line Tool에서 프로그램 작동하도록 구현
  - 터미널에서 실행하면서 인자값을 넘길 수 있다.
  - 첫번째 추가 파라미터로 JSON 문자열이 저장된 파일을 넘겨서 처리하도록 개선한다.
    - 예) $ jsonparser input.json
  - 두번째 추가 파라미터로 처리 결과를 저장할 파일명을 넘기면 결과를 콘솔에 출력하는 대신 파일로 저장한다. 만약 두번째 파라미터가 없을 경우 특정 파일 이름으로 저장한다.
   - 예) $ jsonparser input.json output.json

### 수정사항
- 객체의 의존성 (readCommandLine() / askUserInput())
  - **항상 어떤 메서드가 다른 객체에게 의존하는 흐름을 경계해야한다!**
- Filepath를 가져오는 방법 - Bundle과 FileManager의 차이

### \# 1. 객체의 의존성(1)
> 메소드를 최대한 재사용할 수 있게 만드는 것이 중요하다. 간단한 예로 사용자의 값을 입력받는 용도의 InputView객체의 askUserInput() 메소드를 들 수 있다.

#### Before Code
askUserInput() 함수는 print("분석할 JSON 데이터를 입력하세요.") 문구 때문에 재사용하기 힘든 구조이다.<br/>
askUserInput(message: String) -> String처럼 문구를 외부에 의존하도록 작성하면 재사용이 가능하다.<br/>

```swift
// InputView.swift

func askUserInput () -> String {
        print("분석할 JSON 데이터를 입력하세요.")
        let userInput = readLine()
        let input = userInput ?? ""
        if input == "" {
            return askUserInput()
        }
        return input
    }
```
#### Solution
askUserInput()을 '사용자에게 분석할 데이터를 물어보고 입력받는다'는 역할에 제한을 두지 않고 생각해보면, **readLine()기능을 가지고있고 String을 리턴하는 함수라고 좀 더 일반화해서 생각할 수 있다.** 하지만 현재 함수 내에 안내문구를 출력하는 부분때문에 재사용하기 힘들다.
<br/>만약 출력부분을 지우고 재사용하기 쉬운 구조로 변경하면, 또다시 같은 기능을 하는 함수를 만들지 않고 해당 기능이 필요한 곳에서 askUserInput()을 호출한 후, 그 리턴값을 필요한 곳에 넘기는 방식으로 만들 수 있다. **필요할 때마다 사용자에게 보여줄 입력 안내 문구를 변경하면서! (파라미터로 넘김으로써)**
<br/>(+ 그리고 이러한 흐름 변경의 역할은 상위 모듈에서 한다고 생각하면 된다. (주로 main))

#### After Code
메소드 내의 메시지 출력부분을 파라미터로 넘겨받는 것으로 수정하고 옵셔널 바인딩 기능 또한 제거해서 더 재사용이 가능하도록 최대한 작은 함수로 수정했다.

```swift
func askUserInput(message: String) -> String? {
       print(message)
       let userInput = readLine()
       return userInput
   }
```

### \# 2. 객체의 의존성(2)
> 객체의 의존성을 없애고 테스트하기 좋은 구조를 만든다.


#### Before Code

```swift
// InputView.swift

    func readCommandLine() -> (I: String, O: String) {
        let arguments = CommandLine.arguments
        var cmdInput = ""
        var cmdOutput = "default.json"

        if arguments.count < 2 {
            cmdInput = askUserInput()
            return (I:cmdInput,O:"")
        }
        if arguments.count == 2 {
            cmdInput = readFile(String(arguments[1]))
            return (I:cmdInput, O:cmdOutput)
        }

        if arguments.count > 2 {
            cmdInput = readFile(String(arguments[1]))
            cmdOutput = arguments[2]
            return (I:cmdInput, O:cmdOutput)
        }

        return (I:cmdInput, O:cmdOutput)
    }

    func readFile(_ file: String) -> String {
        var text = ""

        let inputFile = file.split(separator: ".")
        let inputFileName = String(inputFile[0])
        let inputFileType = String(inputFile[1])
        let path = Bundle.main.path(forResource: inputFileName, ofType: inputFileType)
        text = try! String(contentsOfFile:path!, encoding: String.Encoding.utf8)

        return text
    }
```

#### Solution
1. 함수 내부에서 `let arguments = CommandLine.arguments`방식으로 사용하여 CommanLine객체에 의존하는 구조. CommandLine객체의 기능을 실행해야하니 테스트할 수가 없다. <br/> `let arguments를 readCommandLine()`의 인자로 넘기고, 테스트 할때는 `let arguments`와 같은 타입을 외부에서 인자로 넘기면서 테스트 할 수 있다.
2. 현재 `readFile()` 함수는 commandLine에서 입력받은 값을 `.`을 기준으로 `파일이름.확장자`를 나눠서 문자열을 넘겨받고, 그 문자열을 조합한 파일이름을 가진 파일을 **현재경로에서만 읽어올 수 있다.** 이 메소드또한  현재 위치에 있는 특정 파일만 읽어올 수 있다는 문제점이 있다.<br/>
`readFile()` 함수에서 현재 위치를 직접 알아오기 보다는 **파일명과 함께 저장 경로를 매개변수로 받는 구조로 변경해야**, 원하는 경로 어디서든지 파일을 읽어올 수 있는 좀 더 완벽한 `readFile()` 함수가 될 수 있음.


#### After Code
1. 파라미터 arguments를 상위모듈에서 넘겨받는 구조로 변경. 테스트시에도 테스트 환경을 set up할때 CommandLine객체를 외부에서 넘기는 방식으로 코드를 작성하고 `readCommandLine()`을 테스트하면 된다.
2. `readFile()`에서 현재경로를 알고 있고 `파일이름.확장자`만 넘겨받아 조합하는 것이 아닌, 파일경로를 알고싶은 상위모듈이 현재경로와 파일이름, 확장자를 조합해서 하나의 완벽한 경로를 만든 후 `readFile()`에는 완성된 경로만 넘겨서 파일을 읽어오는 구조로 변경

```swift
func readCommandLine(_ arguments: [String]) throws -> (contents: String, outputPath: String, type: InputType) {
        let fileManager = FileManager.default
        let currentDirectory = fileManager.currentDirectoryPath + "/"

        if arguments.count < 2 {
            let userInput = askUserInput(message: "분석할 JSON 문자열을 입력하세요.")
            guard let inputString = userInput else {
                throw InputError.voidInput
            }
            return (contents: inputString, outputPath: "", type: InputType.console)
        }
        if arguments.count == 2 {
            let inputFileContents = try readFile(currentDirectory + String(arguments[1]))
            let outputPath = currentDirectory + "default.json"

            return (contents:inputFileContents, outputPath: outputPath, type: InputType.file)
        }
        if arguments.count > 2 {
            let inputFileContents = try readFile(currentDirectory + String(arguments[1]))
            let outputPath = currentDirectory + String(arguments[2])

            return (contents: inputFileContents, outputPath: outputPath, type: InputType.file)
        }
        return (contents: "", outputPath: "", type: InputType.console)
    }

    func readFile(_ path: String) throws -> String {
        let contents = try String(contentsOfFile: path, encoding: String.Encoding.utf8)
        return contents
    }
```

### FileManager / Bundle
> 파일경로를 읽어오고 결과용 파일을 저장하는 기능을 구현하다가 FileManager와 Bundle을 둘 다 쓰게 되었는데(결국 파일매니저만 쓰는 것으로 수정) 둘의 차이를 간단히 정리했다.

Swift 애플 공식문서에는 아래와 같이 설명되어있다. 오히려 해석해놓으면 뜻이 더 모호하게읽혀서 원문 그대로 중요한 부분만 가져옴.

#### File System(Foundation>File System>FileManager)
> File System : Create, read, write, and examine files and folders in the file system.[(Go to  Official developer.apple)](https://developer.apple.com/documentation/foundation/filemanager)

An FileManager object lets you examine the contents of the file system and make changes to it. The FileManager class provides convenient access to a shared file manager object that is suitable for most types of file-related manipulations. A file manager object is typically your primary mode of interaction with the file system. You use it to locate, create, copy, and move files and directories. You also use it to get information about a file or directory or change some of its attributes.

#### Bundle(Foundation>Resources>Bundle)
> Resources : A representation of the code and resources stored in a bundle directory on disk. [(Go to Official developer.apple)](https://developer.apple.com/documentation/foundation/bundle)

Any executable can use a bundle object to locate resources, either inside an app’s bundle or in a known bundle located elsewhere. <br/>
You don't use a bundle object to locate files in a container directory or in other parts of the file system.

***
위의 내용을 보면 번들객체는 파일매니저와 다르게 컨테이너 경로나 파일시스템의 다른 부분에 파일을 저장시키기위해 사용되진 않는다고 한다. <br/>대신에 같은 앱, 같은 번들 내에서만 읽고쓰기할때만 사용가능하다고 설명한다. iOS는 App단위로만 접근이 가능하도록 제한되어있는데 이게 번들단위로 제한되어있어서 그렇다고 한다.
- 따라서 번들은 앱 안에서 필요한 리소스들, UI를 위한 이미지라든가 폰트같은 것들을 묶고 그것들을 사용하기에 용이하게하는 단위의 개념이다.
- 이와 달리 파일매니저는 파일 시스템 내의 분산된 컨텐츠들에 접근하고 사용하는 동작에 관련된, 더 전반적인 개념이다. 파일매니저 클래스는 대부분의 파일을 처리하는 동작을 편리하게 할 수 있도록 해준다고한다.
- **이런 의미라면 JSON에서는 FileManager를 쓰는게 의미가 맞아서 FileManager객체를 사용했다.**



### 그외 JSON step6 피드백/수정사항 주요내용

- 에러핸들링 / 흐름제어 : main에서 while문을 추가하고 catch블럭에서 break를 이용해서 에러상황에서의 흐름제어는 했지만, 마지막에 정상적인 케이스일때 break를 안넣어서 완전 프로그램이 무한루프를 돌아서...깜짝놀랐다.
  - while을 쓰면 꼭 마지막로직까지 정상적으로 실행됐을때도 break를 넣어서 흐름 제어를 하자!
- 함수를 간단하게: 함수는 어떤 위치에서든 재사용할 수 있는 함수로 만들도록 노력하기.
  - 만약 함수 내에서 switch-case를 써서 무언가를 비교하고 그 안에서 함수를 실행한다면, 심지어 모든 케이스에서 필요없는 인자까지 한꺼번에 받아서 처리하고있다면 **뭔가 잘못된 함수** 임을 인지해야한다!
  - 함수를 최대한 심플하게 만들면, 오히려 main에서 함수를 호출하는 코드가 길어질지라도 호출-리턴하는 관계가 직관적으로 되기 때문.
- OutputView의 showResult()함수에서 필요없는 인자 정리
  - main이 복잡해지더라도, 함수 내부에서 switch문을 써서 무언갈 판단하고 다르게 동작해야하는 로직이 있으면 그 함수를 간단히 만드는게 더 중요함. 지금 상황에서는 main에서 outputType(console or make file)을 알고있으니, OutputView에서 함수를 하나만 부르는걸로 코드를 짜서 main을 간단하게 만들게 아니라 직접 main에 있는 정보를 가지고 바로 필요한 함수를 호출하는게 더 심플하고 알아보기 쉬운 구조임.


### 추가) 질문거리
- guard 옵셔널체이닝. case1과 case2의 차이!
 ```swift
// case 1

 let userInput = inputView.askUserInput(message: "분석할 JSON 문자열을 입력하세요.")
guard userInput != nil else {
    throw GrammarChecker.FormatError.invalidInput
    }
commandLineInput = (input: userInput, output: "")
// userInput이 계속 String?으로 인식

// case 2
let userInput = inputView.askUserInput(message: "분석할 JSON 문자열을 입력하세요.")
guard let inputString = userInput else {
    throw GrammarChecker.FormatError.invalidInput
    }
commandLineInput = (input: inputString, output: "")
//inputString이 String으로 인식!

 ```


### 추가) JSON step6 에피소드...
> 참으로 바보같은 에피소드이기도 하지만 나름의 교훈이 있어서 기재..

- 흐름제어를 처리하다가 debug부분에서 제대로 돌어가지가 않았다. 브레이크 포인트를 써도 로직 상 문제가 없어서, 당최 어디서 에러가난건지도 모른채 프린트를 하나씩 찍으면서 따라가다가...결국 grammar, input, resultData까지 가서 프린트를 찍어봤다!!!!!!!!!!!!!!!! 그러다가 input.json파일을 열었는데 리턴이 한 줄 더 들어가있었다. 이상했다!!!!!!!!!! 너무이상해서 텍스트편집기에서 json패턴을 써보고 저장했는데!!!!!!!!!!!! 결국 아톰이 글을 저장하면서 엔터를 넣어서 저장을했던것이야!!!!!!!!!!!!!!!!그래서 안됐던것이다!!!!!!!!!!!ㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠ멘붕 _(이것때문에 거의 두시간을 날렸다.)_
- 그래서 결국 고민하다가 디버그하면서 수정하다 만 새코드로 돌아갈까, 아니면 돌아가기는 했었던 피드백 당시의 코드로 돌아갈까 고민하다가 그냥 수정하다 만 코드로 돌아갔다. (`git reset --hard` 옵션씀...)
- **교훈 : 커밋 메시지를 잘쓰자. 커밋메시지를 구체적으로쓰자!!**
