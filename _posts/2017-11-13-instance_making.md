---
layout: post
title: 객체를 만들때 두 가지 방법의 차이 (Data Object의경우)
tags: swift 인스턴스 객체
categories: Swift
---



오늘 main에서 객체를 만들때 두 가지 다른 방법을 사용했다. 

```swift
//main.swift

var inputView = InputView()
var gameOption = GameOption(inputView.userInput()) 

//userInput()의 리턴값만 값으로 넘겨주고  GameOption의 init이 실행되면서 인스턴스가 만들어짐.
```

이렇게 하면 userInput의 리턴값 객체가 만들어지고 그 다음 GameOption의 init이 실행되면서 객체가 만들어지는 (값만 넘기면 되는데 어떻게 보면 중복되는)과정이 실행됨.

 

하지만 만약 userInput()이 GameOption을 리턴하게 설계하면 main에서 두 인스턴스를 이렇게 호출할수있음

```swift
//main.swift
//inputView와 gameOption부분만 보자!

var inputView = InputView()
var gameOption : GameOption = inputView.userInput()
var ladderGame = LadderGame(gameOption)
var ladderInfo : LadderInfo = ladderGame.genarateLadder()
var result = ResultView(ladderInfo)
```

이때 userInput()의 리턴값은 GameOption을 리턴하도록 설계해야한다.

```swift
//InputView.swift

struct InputView {
    
    func userInput() -> GameOption {
        print("참여할 사람 이름을 입력하세요. (이름은 쉼표(,)로 구분하세요)")
        let players = readLine()
        let playerNames = players!.split(separator: ",").map(String.init)
        print("최대 사다리 높이는 몇 개인가요?")
        let ladderHeight = Int(readLine()!)
        let inputValue = (playerNames, ladderHeight!)
        let gameOption = GameOption(inputValue)
        
        return gameOption
    }
```

이렇게 하면 userInput의 리턴값이 바로 GameOption타입으로 리턴되면서 인스턴스가 만들어짐. 위에서 발생하는 중복상황*(userInput의 리턴값이 어떤 다른 객체로 리턴되는)*을 줄일 수 있음. 

사다리게임에서 나는 주로 Data Object의 인스턴스를 만들때 로직 객체에서 이 인스턴스를 만드는 방식으로 설계했고, Data 객체가 로직 객체로 넘어갈때는 로직객체의 필드값을 필요한 Data Object로만 정해서 객체를 넘겼다.(LadderGame이나 ResultView 객체 만드는 부분)

  











