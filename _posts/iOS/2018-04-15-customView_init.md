---
layout: post
title: 커스텀 뷰 클래스에 속성 지정하기, UIView의 생성자 - init(), awakeFromNib()
tags: iOS view 커스텀 UIView awakeFromNib
categories: iOS
---

### 커스텀 뷰 만들기의 장점
만약 하나의 뷰 컨트롤러에 여러 UIImageview(혹은 UIView) 타입의 서브뷰를 추가해야 할때, **그 여러 서브뷰들의 크기나, 테두리, 둥근 모서리와 같은 속성이 모두 동일한 경우가 있을 것이다.** 만약 이 때 모든 서브뷰의 속성을 상위 뷰 컨트롤러에서 지정해준다면 반복되는 코드가 발생할 수 있고 뷰 컨트롤러의 코드가 비효율적으로 길어질 것이다.
<br/>
이를 방지하기 위해서는, ***UIView를 상속받는 커스텀 코코아터치 클래스를 하나 만들어서, 내가 원하는 속성을 지정해주고, 추가할 서브뷰들의 타입을 커스텀뷰 타입으로 지정*** 해주면 된다.
<br/>
여기서 ***서브뷰를 추가하는 방법에따라(뷰를 생성하는 방법에 따라) 호출되는 UIView의 초기화 함수가 결정되기 때문에, 커스텀뷰의 어디에 속성설정 코드를 넣어야 하는지가 중요하다!***


### UIView의 생성자
위에서 언급했듯이 뷰가 생성되는 방법에 따라 호출되는 초기화함수를 알아놓으면 이해가 쉬울 수 있어서 [스탠포드 iOS강의](https://www.inflearn.com/course/stanford-ios-%ED%95%9C%EA%B8%80%EC%9E%90%EB%A7%89-%EA%B0%95%EC%9D%98/) 중 관련 부분을 정리해보았다.
- UIView는 두개의 필수 생성자가 있다. 바로 `init(frame: CGRect)`과 `init(coder: NSCoder)`이다.
- `init(coder: NSCoder)`: **스토리보드** 에서 나오면서 UIView를만들때 만들어지는 생성자.
  - 뷰를 스토리보드에서 드래그해서만들었는데, 만약 앱이 실행될때 스토리보드가 재구성되면 이 생성자가 호출되면서 UIView가 만들어짐.
- `init(frame: CGRect)`: **코드로** 뷰를 만들때 호출됨. *반드시 CGRect타입으로 프레임을 정해주고 UIView를 생성*
  ```swift
  let rect = CGRect(x: 10, y: 10, width: 100, height: 100)
  let myView = UIView(frame: rect)
  ```

### 커스텀뷰에 뷰 속성 지정하기
만약 *커스텀뷰를 상속받은 모든 뷰에 둥근 모서리(cornerRadius)속성을 부여하고 싶은* 상황일 때는 아래의 방법들이 있다.

#### addSubVIew()로 서브뷰를 추가했을때
- 코드로 추가한 view에 속성을 적용하기 위해서는 `init(frame:)`에 속성을 지정해줘야한다.
- 다만, `init(frame:)`에는 무조건 frame값을 넣어줘야하기때문에, 아래 예시코드와 같은 방법으로 convenience init에 임시 frame값을 넣어줘도 된다.
  - 이 방법을 사용하면 원하는 frame값은 상위모듈에서 바꿔줘야한다.

```swift
override init(frame: CGRect) { // by code
    super.init(frame: frame)
    self.layer.cornerRadius = 5.0
    self.clipsToBounds = true
}

convenience init() {
    self.init(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
}
```

#### 인터페이스 빌더에서 서브뷰를 추가했을 때
- 인터페이스빌더에서 custom클래스로 지정한 뷰는 `required init?(coder aDecoder:)`가 호출된다.
- (스토리보드에서 뷰 컨트롤러가 가진 뷰의 타입을 오른쪽 탭 custom class에서 내가 추가한 커스텀뷰 클래스 이름 입력하여 연결)
- *만약에 어디서 호출될지 모르면 awakeFromNib과 두 convenience init에 설정하는 코드를 넣어주면 된다.* 는 꼼수아닌 꼼수도 있다..

```swift
override func awakeFromNib() {
    // config setting code
        super.awakeFromNib()
    }

required init?(coder aDecoder: NSCoder) {
    // config setting code
    fatalError("init(coder:) has not been implemented")
}
```

#### \+ 추가. awakeFromNib()
> awakeFromNib()은 인터페이스 빌더에서 뷰가 만들어졌을때 호출된다고만 알고있었는데, 그보다 더 자세하게 알 필요가 있어서 추가

#### awakeFromNib이 호출되는 시점
> `awakeFromNib()`은 객체가 초기화(인스턴스화)된 후 호출된다.

- UIView에서 상속받은 커스텀 View 클래스를 만들고 인터페이스빌더에 지정하면(오른쪽 탭 inspector에서 지정하는 방법으로) 커스텀 클래스는 아카이브되어 있다가 언아카이브됨(xib파일에)
- 이 때 init(coder:)가 호출되고 내부 속성이 초기화됨
- 일부는 이 때 의미없는 값으로 초기화되거나 IBOutlet을 비롯한 객체 참조 관계가 불명확할 수 있음
  - init 시점에서는 frame과 관련된 크기, 위치 등 뷰가 완벽하게 만들어지지 않은 상태
  - 이와같이 인터페이스 빌더에서 연결된 객체의 변수를 사용하려고 할때 `awakeFromNib()`을 사용함
  - 이렇게 `awakeFromNib()`은 객체를 인스턴스화 한 후(`init()`이 호출된 후) 호출된다.
- (IBOutlet이 없어도 `awakeFromNib()`은 호출됨)

#### awakeFromNib()과 init()에서 모두 설정해야 하는 이유
> `뷰를 만들고 커스텀뷰를 인터페이스빌더에서 연결했을땐 init(frame:), init(coder:)와 awakeFromNib()두 군데 모두에서 설정을 해준다`의 의미

- 그래서 초기화가 끝나고 설정 awakeFromNib 시점에서 추가적인 작업이 필요한 경우 해야함.
- 커스텀 뷰를 만드는 개발자 입장에서는 다른 개발자가 `init(frame:)`을 호출해서 코드로 생성할지, 인터페이스 빌더에서 지정해서 사용할지 불명확하니까 코드로 만들던 인터페이스빌더로 작업하던 둘 다 동일한 동작이 가능하도록 지정하는 게 좋음
- JK의 코멘트 추가
```
경험상 init(coder:) 시점에는 frame 이나 Layer 관련없는 값들,
awakeFromNib 시점에는 frame 이나 Layer 관련된 값들을 설정하도록 구현하면 됩니다.
init(frame:) 은 이미 frame 정보가 있으니까 괜찮구요.
물론 autoLayout 처리를 하는 경우는 awakeFromNib 보다 더 뒤에 해야합니다.
```


[참고링크 1](http://joejeon.tistory.com/702), [참고링크 2](https://stackoverflow.com/questions/9122344/when-does-awakefromnib-get-called?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa), [참고링크 3](http://jjun5050.tistory.com/12)
