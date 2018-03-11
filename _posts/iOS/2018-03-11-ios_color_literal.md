---
layout: post
title: UIColor클래스 - 커스텀 Color Literal사용하기
tags: UIColor colorLiteral iOS textcolor
categories: iOS
---

#### textColor 프로퍼티

iOS개발에서, UILabel클래스의 textColor속성의 선언은 이렇다.
- `var textColor: UIColor! { get set }`

이러한 textColor속성을 사용할때 가장 간편한 방법은 애플에서 제공하는 미리 정해놓은 UIColor클래스의 타입프로퍼티를 사용하면 된다. 예를들어, 화면에 보여지는 텍스트 색을 초록색으로 하고싶다면, 아래와 같이 쓸 수 있다.
```swift
class FirstViewController: UIViewController {

    @IBOutlet weak var photoLabel: UILabel!

    override func viewDidLoad() {
        super.viewDidLoad()
        self.photoLabel.text = "JINiOS"
        self.photoLabel.textColor = UIColor.green
    }

```
이렇게 애플은 기본적인 색깔들은 이미 literal하게 만들어놓았다.
<br/>
**하지만 커스텀된 다른 색을 사용하고싶다면!?** 코드로 하는 방법도 있지만 이번엔 간단하게 GUI의 도움을 받아 색깔을 커스텀할 수 있는 방법을 정리해봤다.

#### Custom Color Literal사용하기

-  먼저 변수를 선언하고, `Color Literal`이라고 써준다. xcode는 아래와 같이 드롭다운으로 `Color Literal`을 보여준다.
<br/>

![xcode_color_literal](/assets/img/post_img/colorLiteral_20180311.png)
-  `Color Literal`을 선택하면 컬러를 선택할 수 있는 창이 뜨고, others 버튼을 클릭해서 직접 색상과 투명도를 선택하고 변수 선언을 완료한다.
<br/>

![xcode_color_literal2](/assets/img/post_img/colorLiteral2_20180311.png)
-  선언된 변수는 아래와 같이 사용한다.

```swift
@IBOutlet weak var firstDescription: UILabel!

override func viewDidLoad() {
    super.viewDidLoad()
    self.firstDescription.text = "사진액자 firstDescription"
    let colorLiteral = #colorLiteral(red: 0.1380051349, green: 0.3703911386, blue: 1, alpha: 0.7430436644)
    self.firstDescription.textColor = colorLiteral
  }
  // colorLiteral변수가 코드블럭으로 붙여넣으면서 rgb값이 숫자로 나왔음
```

  실제로는 `colorLiteral`변수는 코드에 설정된 색깔과 함께 보여지게된다.
<br/>

  ![xcode_color_literal3](/assets/img/post_img/colorLiteral3_20180311.png)
