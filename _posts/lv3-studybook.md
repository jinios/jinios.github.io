## 뷰 계층구조
- iOS앱은 모든 뷰들의 컨테이너 역할을 하는 UIWindow 인스턴스를 하나 가진다. (UIWindow는 UIView의 하위 클래스이므로 그 자체가 하나의 뷰다.)
- 앱이 실행되고 윈도우가 만들어지면 윈도우에 다른 뷰들을 추가할 수 있다.
- 윈도우에 추가한 뷰는 윈도우의 하위 뷰라고 한다. (subview)
- 앱에 보여지는 컨텐츠를 바꾸기 위해서는, 뷰컨트롤러를 사용하여 윈도우에 상응하는 뷰로 바꿀 수 있다. 윈도우 자체를 스스로 바꿀 수는 없다.
- UIWindow는 UIApplication객체를 통해서 뷰에서 발생한 이벤트를 뷰컨트롤러로 전달한다.

## MVC
- 뷰와 모델은 독립적이다. 서로 분리되어있음.

## 콜백함수
- 사용자에 의해 호출되는 것이 아닌 특정 함수에서 호출돼 필요시 코드 내에서 사용되는 함수.
- 호출된 함수를 알려주어, 다른 프로그램 또는 다른 모듈에서 함수를 호출 하게 하는 방법

## 뷰 생명주기
뷰 생명주기의 기본 flow는
- ***viewDidLoad > viewWillAppear > viewDidAppear > viewWillDisappear > viewDidDisappear***
(단, viewDidLoad는 rootView이기때문에 한번만 호출되는 것이 일반적이다. [참고링크](http://zeddios.tistory.com/43))

FirstView - BlueView - NavyView - FirstView로 연결된 flow일때, `print(#file, #line, #function, #column)`로 콘솔에 표시
```
.../FirstViewController.swift 27 viewDidLoad() 40 >> 첫번째 뷰 로드
.../FirstViewController.swift 48 viewWillAppear 40 >> 첫번째 뷰가 보일 것이다.
.../FirstViewController.swift 52 viewDidAppear 40 >> 첫번째 뷰가 보임.  
------- 버튼 터치 ------- (FirstView -> BlueView)
.../BlueViewController.swift 15 viewDidLoad() 40 >> 첫번째 뷰에서 다음버튼 터치 순간실행. BlueViewController 차례시작, 다음(blue)뷰가 로드되고
.../FirstViewController.swift 56 viewWillDisappear 40 >> 이전(first)뷰가 사라질 준비
.../BlueViewController.swift 28 viewWillAppear 40 >> 다음 뷰가 로드될 준비
.../BlueViewController.swift 32 viewDidAppear 40 >> 다음 뷰 나타남
.../FirstViewController.swift 60 viewDidDisappear 40 >> 이전 뷰 사라짐
------- 버튼 터치 ------- (BlueView -> NavyView)
.../NavyViewController.swift 15 viewDidLoad() 40 >> BlueView에서 다음 버튼 터치 순간실행. NavyView차례 시작. NavyView 로드
.../BlueViewController.swift 45 viewWillDisappear 40 >> BlueView는 사라질 예정
.../NavyViewController.swift 32 viewWillAppear 40 >> NavyView가 나타날 준비
.../NavyViewController.swift 36 viewDidAppear 40 >> NavyView가 나타남
.../BlueViewController.swift 49 viewDidDisappear 40 >> BlueView 사라짐
------- 버튼 터치 ------- (NavyView -> FirstView)
.../FirstViewController.swift 27 viewDidLoad() 40 >> FirstView 로드
.../NavyViewController.swift 40 viewWillDisappear 40 >> NavyView가 사라질 예정
.../FirstViewController.swift 48 viewWillAppear 40 >> FirstView가 나타날 준비
.../FirstViewController.swift 52 viewDidAppear 40 >> FirstView가 나타남
.../NavyViewController.swift 44 viewDidDisappear 40 >> NavyView 사라짐

```
**중요** : 다음`(Next)`뷰는 이전`(Previous)`뷰가 사라진 다음에 나타나는 것이 아니라, **다음 뷰가 나타나고(viewDidAppear) 그 이후에 이전 뷰가 사라진(viewDidDisappear)다!**
**비교** : 앞서말한 [참고링크](http://zeddios.tistory.com/43)의 설명과 달리 FirstViewController가 NavyViewController로부터 재호출 될때 viewDidLoad가 한번 더 호출 된 이유는, FirstViewController를 호출할때 `instantiateViewController(withIdentifier:)`를 사용했기 때문인 것 같다. <br/>이 메서드는 호출하고싶은 뷰 컨트롤러의 인스턴스를 호출할때마다 만든다는 특징이 있다.(This method creates a new instance of the specified view controller each time you call it. [참고](https://developer.apple.com/documentation/uikit/uistoryboard/1616214-instantiateviewcontroller))


# View
### 뷰를 코드로 만드는 방법 예시
- addSubVIew()
- removeFromSuperView()와 같은 메서드들이 있다.
뷰 컨트롤러는 뷰를 가짐. 뷰는 최상위계층에 있는 UIView를 가리키는 포인터
UIView초기화 :
awakeFromNib()
bounds - 뷰 드로잉과 관련
frame - 슈퍼뷰안에서 나의 뷰가 어디있는지를 나타냄. (frame은 직사각형이고 CGRect임) frame은 슈퍼뷰의 좌표계 상에서 나의 뷰를 완전히 덮는 직사각형, 그래서 내가 만약 어떤 뷰를 어떤 곳에 놓으려고 하면 **슈퍼뷰 안의 어느 곳에 그 뷰가 들어갈지 정해줘야한다.** 이것은 그 뷰의 frame을 지정해줘서 그렇게 할 수 있다. 그리고 당연히 이것은 슈퍼뷰 안에 넣는 것을 얘기하고있으므로, 슈퍼뷰의 좌표계 내에서 지정해줘야 한다. (자체 뷰 안의 좌표계에서 지정해주는 것이 아님)
center - 내 뷰의 정중앙을 의미하는 것이 아님! 슈퍼뷰의 좌표계를 기준으로 정중앙을 표현한 것임.
frame과 center는 내 뷰의 위치를 지정해주는 것이지, 드로잉과는 전혀 상관없음. 그릴때는 바운드를 사용해야함.
드로잉코드에는 frame이나 center를 사용하지 말것!
회전한 상황에서 - 바운드는 더 작고 frame은 크다(회전 시에 frame - 회전한 bound를 다 담을 수 있는 가장 작은 직사각형크키)

### 코어그래픽
- 그릴 수 있는 컨텍스트가 필요함 context
- path를 만듦
- 이 경로를 그리는데 속성을 설정함
### UIBezierPath클래스
- moveToPoint(), addLineToPoint(), closePath()같은 메소드로 경로를 만듦
- UIColor같은 클래스에 선의 너비나 색깔같은 속성을 준다.

# 뷰 컨트롤러 컨테이너
### 바람직한 iOS 앱의 뷰 구조
- 윈도우
  - 뷰 컨트롤러 1
    - 뷰
  - 뷰 컨트롤러 2
    - 뷰
위에서 뷰 컨트롤러 1과 뷰 컨트롤러 2를 연결해 주는 게 네비게이션 컨트롤러 같은 컨테이너 컨트롤러

### 컨테이너 컨트롤러
실제로 화면에 내용을 표시하지는 않으면서 내용을 표시하는 컨텐트 컨트롤러들간의 전환을 관리하는 컨트롤러

- 네비게이션 컨트롤러
- 탭 바 컨트롤러
- 페이지 컨트롤러 등

# 뷰 컨트롤러
뷰 컨트롤러를 스크린에 나타나는 방법은 두가지가 있다. 컨테이터 뷰 컨트롤러에 장착(?embed)하거나 나타내는 것이다. 컨테이너 뷰 컨트롤러는 앱의 가장 주요한 내비게이션을 제공하지만, 화면에 보여지는 뷰 컨트롤러또한 중요한 내비게이션 툴이다.
뷰 컨트롤러를 화면에 나타내는 것은 UIVIewController 클래스가 하는 일이고, 이건 모든 뷰 컨트롤러 객체에 해단된다. 뷰 컨트롤러를 화면에 나타내는 것은 현재의 뷰 컨트롤러(현재 화면에 나타나있는 뷰 컨트롤러)와의 관계를 만든다는 뜻이다. (presenting, presented)


# 뷰 컨트롤러 컨테이너
뷰 컨트롤러와 뷰의 관계
ios의 MVC에서 C는 viewControllers를 말한다
뷰 컨트롤러를 child로 가지는 뷰 컨트롤러(ex. UITabBarController, UINavigationController, SplitViewController)
뷰 입장에서는 뷰 컨트롤러가 중요하지 않지만, 뷰 컨트롤러 입장에서 보면 뷰는 반드시 하나는 있어야 한다. 다만 뷰를 추가하는것보다는 VC를 단위로 추가해야한다고 하는 것은 만약 view가 대체되는(연관이 있는)화면이라면 addSubVIew를 할 수 있지만 서로 아예 다른 view라면 각각의 view마다 VC가 있어야 한다는 뜻
![screenshot_step5-1](./Screenshot/containerVC.png)


## 프레임워크
> 프레임워크는 기본적으로 어떤 것을 이루는 뼈대, 구조를 의미하는데, 소프트웨어에서의 프레임워크는 애플리케이션 제작을 빠르고 편리하게 할 수 있도록 미리 뼈대를 이루는 각종 코드를 제작하여 모아둔 것. 예를들어 버튼용 클래스를 미리 만들어둔 UIKit프레임워크를 사용하면 내가 따로 버튼을 구현할 필요가 없다!

UIXXX형식으로 시작하는 클래스는 UIKit 프레임워크가 제공하는 클래스.
따라서 UIApplication, UIButton등은 스위프트 언어 자체에서 제공하는 것이 아닌 프레임워크를 통해 제공되는 클래스이다.
따라서 소스코드 상단에 특정 프레임워크를 반입시켜주는 `import UIKit`을 기재해주는것

예를들어, 앱을 만들기 위해 필요한 프레임워크는 UIKit뿐만 아니라 파운데이션프레임워크(네트워크나 날짜연산 기능 처리), 코어 애니메이션 프레임워크(애니메이션 처리), 웹과 관련된 기술 구현(웹킷 프레임워크), 주소록 관련은 AddressBook UI 프레임워크, 사용자 알림을 위해서는 UserNotifications 프레임워크 필요
이 각각의 계층을 거슬러 올라가면 코코아터치라는 하나의 거대한 프레임워크가 나타남. 앱을 만들고 실행할 때 필요한 iOS기반 기술들은 모두 코코아터치 프레임워크를 통해 구현됨

***
### <꼼꼼한 재은씨 읽고 내용정리>


## 코코아 터치 프레임워크
> 애플환경에서 터치 기반의 애플리케이션을 제작하기 위한 도구들의 모음. 이들 중에서 가장 대표적인 것이 User Interface이다. 다양한 앱들이 서로 다른 내용과 기능에도 불구하고 화면 구성에서 일정 수준 이상의 유사성을 갖는 것 또한 이들이 모두 동일한 유저 인터페이스 도구를 이용하여 화면을 구현하기 때문이다.

코코아터치 프레임워크중에는 앱을 만드는데에 필수 요소가 아닌 것들이 있다. (ex. iAd Framework, 앱 안에 광고를 실을 수 있게 지원)
이런 프레임워크는 사용을 위해서 별도 설정과정을 거치게 되어있는데, UIKit와 Foundation 프레임워크는 자체만으로 방대하고 필수적인 도구들이라서 코코아터치 프레임워크를 이루는 주 프레임워크라고 간주한다.
- Foundation 프레임워크 : 기본 데이터 형식, 컬렉션 및 앱의 기본 객체와 기반 기술을 제공하는 역할 (앱의 내부적으로 돌아가는 기능)
- UIKit 프레임워크 : 유저 인터페이스 도구를 통해 iOS앱을 구현할 수 있는 방법을 제공 (iOS앱으로서의 특징적인 부분 담당)

#### 코코아 프레임워크 / 코코아 터치 프레임워크
코코아터치 프레임워크는 기존 매킨토시 환경에서의 개발도구인 코코아 프레임워크를 기반으로 만들어졌지만,(실제로도 많은 부분을 공유하고 있다.) 데스크톱 환경과 터치 환경용 인터페이스는 완전히 다르기때문에 코코아에서 사용되는 앱킷프레임워크는 UIKit 프레임워크로 대체되었다. 현재 macOS를 제외한 iOS, watchOS, tvOS는 코코아터치프레임워크를 기반으로 만들어진다.


### iOS UI의 표현구조
iOS는 항상 디바이스 스크린에 꽉 들어차는 하나의 화면만을 표시할 수 있으며, 다른 프로그램이 실행되면 기존의 화면이 내려가고 그 자리를 새로운 화면이 대신 채운다. 이런 과정은 윈도우와 뷰 객체가 사용된다.
- 윈도우 : iOS 스크린을 빈틈없이 채우기 위한 객체로, 항상 유저 인터페이스 표현 계층의 최상위에 위치함. 뷰의 일종이지만 직접 콘텐츠를 가지지는 않으며 콘텐츠를 가진 뷰를 내부에 배치하여 화면에 출력하는 역할을 한다. 화면이 전환되더라도 윈도우 객체는 전환되지 않으며 내부에 배치된 뷰의 콘텐츠만 변경됨
- 뷰 : 콘텐츠를 담아 이를 스크린상에 표시하고, 사용자의 입력에 반응. 윈도우의 일부를 자신의 영역으로 정의하고, 여기에 필요한 콘텐츠를 채워넣어 스크린에 나타냄. 동시에, 윈도우로부터 전달된 사용자의 입력에 반응하여 그에 맞는 결과처리
- 윈도우 내부에는 수많은 뷰가 포함되어있다.
- 윈도우와 뷰 사이는 뷰 컨트롤러를 통해 연결됨.
- 뷰 컨트롤러는 뷰의 계층 관리, 윈도우에 전달
- 윈도우가 뷰를 직접 참조하지 않고 뷰 컨트롤러가 그 사이를 중계하는 구조 - 윈도우는 뷰 컨트롤러를 통해 제공되는 뷰를 읽어들여 표현할 뿐, 뷰를 직접 관리하지 않아도 됨.
- 윈도우 객체는 하나의 뷰 컨트롤러를 루트 뷰 컨트롤러로 지정하여 참조
- 루트 뷰 컨트롤러로 지정되지 못한 나머지 뷰 컨트롤러들은 루트 뷰 컨트롤러의 관리대상으로 연결되거나 다른 방식으로 이어짐. 다만 이들은 직접적인 윈도우 객체의 관리대상이 아니며 윈도우 객체는 항상 루드 뷰 컨트롤러만을 참조한다.
- 스토리보드를 편집하는 대부분의 뷰 컨트롤러들은 각자가 하나씩의 화면을 담당하여 콘텐츠를 표현하고 뷰를 관리한다. (Scene)
- A,B 두 개의 씬으로 이루어진 앱이 있다면 이는 각각의 씬을 담당하는 뷰 컨트롤러가 있다고 생각할 수 있다.
- 콘텐츠 뷰 컨트롤러 : 씬을 담당하고 콘텐츠를 표시하는 뷰 컨트롤러
- 콘텐츠 뷰 컨트롤러간의 관계를 담당하는 컨트롤러도 있다. 바로 컨테이너 뷰 컨트롤러. 예로 내비게이션 컨트롤러나 탭 바 컨트롤러, 페이지 컨트롤러 등이 있음.
- 컨테이너 뷰 컨트롤러의 일부는 다른 뷰 컨트롤러의 관리를 위해 고유한 역할을 하는 특정 객체를 화면에 부분적으로 추가하는데, 내비게이션 바나 탭 바 등이 대표적인 예시임
- 뷰 계층구조 : 뷰 컨트롤러 내부는 수많은 뷰들로 이루어진다. 이들 뷰는 다른 뷰 객체를 포함하는 컨테이너의 역할도 수행할 수 있다. 이를 뷰의 계층구조라고한다 (superView - subview)
- 루트 뷰 : 뷰 계층구조의 최상위에는 하나의 뷰(rootView) 존재. 일반적으로 화면을 전체 채울 수 있는 크기를 유지함
- 일반 뷰 컨트롤러에서는 View객체가 루트 뷰 역할을 담당한다.
- 루트 뷰 내부에는 각자의 크기와 영역, 표현할 콘텐츠를 가진 여러 개의 서브 뷰가 추가되는데, 일부 뷰의 영역은 서로 겹치기도 한다. 루트 뷰는 이와 같은 서브 뷰들을 모아 하나의 전체 뷰를 구성하고, 뷰 컨트롤러를 통해 이를 윈도우에 전달.

## 뷰 컨트롤러
> 뷰 관리, 화면과 데이터 사이의 상호작용 관리 (윈도우에서 이벤트 전달 > 내부 구현 로직 실행 > 로직 실행 결과를 화면에 표시)

### 뷰의 관리
뷰 컨트롤러의 주요 역할 : 뷰의 계층을 관리하는 역할
- 뷰 컨트롤러에는 컨트롤러 내부에 있는 모든 객체의 대장격인 루트 뷰가 하나 존재함
- 일반 뷰나 테이블뷰, 컬렉션 뷰 등 다양한 뷰가 루트 뷰로 지정 될 수 있으며, 스토리보드에서 뷰 컨트롤러 위에 배치하는 대부분의 객체들은 루트 뷰에 추가되는 서브뷰라고 할 수 있음
- 뷰 컨트롤러는 루트 뷰만을 참조하며, 루트 뷰 아래에 있는 뷰들은 각자 자신보다 하위에 있는 뷰를 참조하고 있는 체인같은 구조이기때문에, 뷰 컨트롤러가 루트 뷰만을 참조해도 모든 뷰에 접근 할 수 있다.
- 코드로 뷰를 추가하고 삭제
  - `addSubVIew`: 미래의 슈퍼뷰에게 보내짐, 이 뷰를 너 자신에게 추가해
  - `removeFromSuperView`: 자기 자신에게 보내짐. 수퍼뷰에서 너 자신을 없애

### iOS에서의 화면 전환 개념
- 뷰 컨트롤러 뷰 위에 다른 뷰를 가져와 뷰 바꿔치기 : 컨테이너 뷰 컨트롤러가 사용하는 방식, 혹은 일부 특수한 앱
- 뷰 컨트롤러에서 다른 뷰 컨트롤러를 호출하여 화면전환
- 내비게이션 컨트롤러를 사용하여 화면전환
- Segue 사용하여 화면전환

첫번째를 제외한 나머지 방법들은 뷰 컨트롤러를 호출하는 방식으로 동작함. 전환할 화면을 담당하는 뷰 컨트롤러의 인스턴스를 생성하고 기존의 화면 위에 덮는다.
따라서 **현재 화면 위에 새로운 화면이 얹어져서 참조 관계가 성립** 되는 것이지, **새로운 화면이 현재 화면을 대체한다고 생각하면 안됨!**
이 같은 특성으로 기존 화면과 새로운 화면은 참조 관계가 성립되는데, 화면 전환 방식에 따라 서로 직접 참조가 가능하거나 화면 전환을 관리하는 전담 객체를 통해서 간접적으로 참조하기도 함
- 다음 화면으로 이동하는 방법과 이전 화면으로 되돌아가는 방법이 다름
- 화면 전환 방식에 따라 이전 화면으로 되돌아가는 방법이 다름
  ex) present메소드로 전환했으면 dismiss로 돌아가야함

### 뷰와 모델
- 모델은 컨트롤러에 의해서 해석되어서 뷰에서 표현되어야함(visualization)
- vc클래스에 모델 클래스를 변수 선언
  - 모델의 로직대로 어떤 것이 변경되면 뷰를 업데이트
- didset - 초기화중에 값을 설정하면 didset이 호출되지않는다.
- IBOutlet이 연결될때 didSet을 해줘야만 모델과 연결된 컨트롤러대로 뷰가 업데이트됨..

### MVC연결
multiple MVC examples: UITabBarController, UINavigationController, UISplitViewController


***
# Key Objects of ios
> 자판기미션 시작하면서 정리

## iOS infrastructure
앱은 내가 작성한 커스텀 코드와 시스템 프레임워크 사이에서 상호작용한다. 시스템 프레임워크는 앱이 실행되는데 기본적인 인프라를 제공하고, 내 코드는 그 인프라를 커스텀하도록 설계하여 내가 원하는대로 앱이 보여지도록 만들 수 있다.
iOS프레임워크는 MVC패턴과 델리게이션 같은 디자인패턴을 기반으로 동작한다.  

## iOS앱을 구성하는 핵심객체(UIKit Framework의 핵심 객체)

앱이 시작되는동안, UIApplicationMain 함수는 주요 객체를 세팅하고 앱을 실행을 시작한다. 앱의 가장 핵심적인 역할을 하는 [UIApplication](https://developer.apple.com/documentation/uikit/uiapplication) 객체는 시스템과 앱 내부의 다른 객체들이 상호작용할 수 있도록 한다.
iOS앱은 MVC패턴을 사용하는데, 데이터와 로직을 데이터의 시각화로부터 분리해서 동작시키는 패턴이다.

- **UIApplication** : 앱의 가장 핵심적인 역할을 하는, 앱 그 자체를 의미하는 객체. 시스템으로부터 이벤트를 받아 Custom Objects로 이벤트를 전달하고 상위 레이어의 앱 동작을 조정
  - 앱 상태 변화나 푸시 메세지 도착처럼 특수한 형태의 이벤트를 우리가 정의한 델리게이트 객체에 전달하여 사전에 정의된 메소드를 호출할 수 있도록 한다.
  - UIApplication 객체는 대리인인 AppDelegate 객체를 내세워 커스텀 코드를 처리할 수 있도록 약간의 권한을 부여한다.
  - UIApplication은 앱의 생명주기나 이벤트 처리와 같이 중요한 일들을 담당하고, AppDelegate는 커스텀 코드를 처리하게 된다.
- **AppDelegate** : 위임받은 권한을 이용하여 App의 초기화(app initialization), 상태변화(state transitions), 다른 앱의 기타 이벤트를 핸들링
  - custom 객체로서 앱 런칭시점에 UIApplicationMain 함수에 의해 생성된다. (커스터마이징하거나 서브클래싱 할 수 있도록 오픈되어 있다.)
  - AppDelegate객체 내의 custom logic들은 UIApplication객체와 상호작용한다.
  - 앱이 시작되면 앱 델리게이트는 `application(_:didFinishLaunchingWithOptions:)` 메시지를 받게 되는데, 이 시점에 크리티컬한 데이터를 초기화하면 된다. 단, 앱이 화면에 나타나기 전이므로 시간이 많이 걸리는 작업은 하지 않는 것이 좋다.
  - 앱 내에서 오직 하나의 인스턴스만 생성되도록 보장받는다.
  - 앱 전체의 생명주기와 함께 한다. 앱이 처음 만들어질 때 객체가 생성되고, 앱이 실행되는동안 유지되다가, 앱이 종료되면 소멸한다.
  - AppDelegate 객체는 종종 앱의 초기 데이터 구조를 설정하기 위해 사용되기도 한다. (AppDelegate 객체에 데이터를 저장하면 앱 종료 전까지 데이터를 유지할 수 있으므로)
- **UIWindow** : 화면 그리기 지원 도구를 제공하는 객체.
  - 디바이스 스크린을 빈틈없이 채우기 위한 객체
  - 대부분의 앱은 하나의 윈도우를 가진다.
  - 항상 유저 인터페이스 표현 계층의 최상위에 위치한다.
  - 뷰의 일종이지만 직접 콘텐츠를 가지지 않는다. UIView들이 콘텐츠를 표현하면 디바이스 스크린에 이를 표현한다.
  - 화면이 전환되더라도 윈도우 객체는 전환되지 않는다.(내부에 배치된 뷰의 콘텐츠만 변경된다.) 뷰 컨트롤러를 조정해서 뷰를 변경해야한다.
  - 윈도우는 UIApplication과 상호작용하며, 특정 이벤트가 발생하면 그 이벤트를 뷰컨트롤러에 전달하는 역할을 한다.

- **View controller** : 앱의 컨텐츠가 화면에 보이게 하는 역할
  - 하나의 View Controller 객체는 하나의 뷰와 뷰의 서브뷰를 관리. View Controller 객체는 자신이 관리하는 뷰를 앱의 윈도우에 위치시킴으로써 뷰가 화면에 보여지도록 한다.
  - UIVIewController클래스는 모든 뷰 컨트롤러 객체의 부모클래스이다. 기본적인 시스템 상에서 일어날 수 있는 동작에 따라 뷰의 기본 동작들을 제공한다.

- **View and Control** : 앱의 콘텐츠를 시각적으로 표현
  - 뷰는 명시된 직사각형 내에서 컨텐츠를 draw하고 그 영역 내의 이벤트에 반응한다.
  - 컨트롤은 특수한 뷰인데 예를들어 버튼, 텍스트필드, 토글 스위치처럼 친숙한 인터페이스이다.

## App Loading Process(앱 초기화 과정)
> UIApplicationMain()함수의 역할

1. App 실행: main() 함수 실행
2. main(): main()는 UIApplicationMain() 호출
3. UIApplicationMain(): 앱의 본체에 해당하는 객체인 UIApplication 객체 생성
4. UIApplication 객체: Info.plist 파일을 바탕으로 앱에 필요한 데이터와 객체 로드
5. AppDelegate 객체 생성 및 UIApplication 객체와 연결
6. 이벤트 루프 생성 등 실행에 필요한 준비 진행
7. 실행 완료를 앞두고 UIApplication객체가 AppDelegate에 `application(_:didFinishLaunchingWithOptions:)` 메시지 전송



## The Main function
C를 기반으로 하는 앱과 동일하게 iOS앱도 main함수로부터 실행이 시작된다. 하지만 iOS프로젝트에는 main.swift가 생략되어있다. (Xcode가 기본으로 작성해준다. 99%의 iOS앱 프로젝트가 앱의 론칭 방식까지 변경할 이유는 없기 때문에 우리는 보통 앱이 실행되고 나서 커스텀 코드를 만나게 되는 첫 지점인 앱 델리게이트 파일부터 편집하게 된다.)
```swift
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

int main(int argc, char * argv[])
{
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```
main함수를 보면 알 수 있지만, (함수의 리턴 부분을 보면)UIApplicationMain함수가 앱의 핵심 객체를 생성하고, 스토리보드로부터 앱의 UI를 로드하고, 커스텀 코드를 호출하며, 앱이 run loop 내에서 동작 할 수 있도록 만든다. *(GUI를 사용하기 위한 런루프를 돌려주는 작업)* 이 중에서 개발자가 해야 할 것은 스토리보드 파일과 custom initialization code이다.

***
[꼼꼼한재은씨 스위프트 프로그래밍 3 참고](http://www.yes24.com/24/goods/33262863)
# 앱의 기본 구조
## 엔트리포인트와 앱의 초기화 과정
- C언어에 뿌리를 둔 애플리케이션은 모두 main()으로부터 시작. entry point
- iOS앱에서는 main()함수를 우리가 직접 작성하지 않고, 대신 Xcode프로젝트를 생성하면 자동으로 만들어짐. main() iOS앱이 실행될때 처리해야 할 내용이 작성되어있다.

```swift
#import <UIKit/UIKit.h>
#import "MYAppDelegate.h"

int main(int argc, const char ** argv) {
    @autoreleasepool{
        return UIApplicationMain(argc, argv, nil,   
                 // 앱 델리게이트를 만들 수 있는 힌트를 줘야 한다.
                 NSStringFromClass([MYAppDelegate class]));
    }
}
```

## main()함수가 하는 일
- 실행 시 시스템으로부터 전달받은 두 개의 인자값과 AppDelegate클래스를 이용하여 UIApplicationMain()함수를 호출, 그 결과로 UIApplication객체 반환
- 생성된 UIApplication객체는 UIKit프레임워크에 속해있으므로 이후의 앱 제어권은 UIKit프레임워크로 이관
- main()이 C기반 애플리케이션의 엔트리포인트라면,
- UIApplicationMain()은 iOS앱에 속하는 부분의 엔트리포인트라고 할 수 있다.

## UIApplicationMain()
- UIApplication객체 생성 - 앱의 그 본체라고 할 수 있음
- iOS앱에 속하는 엔트리포인트
- 앱의 핵심객체를 생성하는 프로세스 핸들링
- 스토리보트 파일로부터 앱의 UI를 읽어옴
- 우리가 작성한 커스텀코드 호출
- 앱 생성 초기에 필요한 설정 구현
- 이 과정에서 우리가 구현해야하는 것은 스토리보드 파일이나 초기화를 위한 커스텀코드임

## UIApplication / AppDelegate
- 작성한 커스텀코드나 객체, 앱의 기능이라고 생각하는 모든 것들은 다 UIApplication에 포함되어 있는 하위객체임
- 모바일디바이스에 설치된 앱을 실행하면 초기 구동과정을 거쳐 앱 프로세스가 메모리에 등록됨. **이때의 앱 프로세스가 UIApplication객체임**
- 역할
  - 이벤트 루프나 다른 높은 수준의 앱 동작을 관리 (앱 생명주기 관리나 이벤트 처리)
  - 푸시알림과 같은 특수한 이벤트를 커스텀객체인 델리게이트에게 알려줌
  - 이 UIApplication클래스를 특별한 목적이 있는 경우가 아니라면 서브클래싱 없이 그대로 사용가능
  - 서브클래싱할 필요도 없고, 하기도 어렵다.
  - 하지만 의도와 목적에 맞게 특별히 처리해야 할 경우, 서브클래싱 없이 사용하기는 힘든데, 이럴때 **AppDelegate** 라는 대리객체에 커스텀 코드를 처리할 수 있도록 권한 부여함

## AppDelegate
- AppDelegate는 UIApplication에게서 위임받은 권한을 이용하여 커스텀코드와 상호작용
- 커스터마이징과 서브클래싱에 오픈되어있음
- iOS애플리케이션 내에서 오직 하나의 인스턴스만 생성되도록 보장.
- 앱이 처음 만들어질때 생성, 앱이 실행되는동안 계속 유지, 종료될때 소멸
- AppDelegate객체에 데이터를 저장하면 앱이 종료될때까지 계속 데이터를 유지할 수 있다.
- AppDelegate객체는 종종 앱의 초기 데이터 구조를 설정하기위해 사용된다.

## 앱이 실행되는 과정
> UIApplication객채와 AppDelegate객체 연관되어 앱이 실행되는 과정!

1. App 실행: main() 함수 실행
2. main(): main()는 UIApplicationMain() 호출
3. UIApplicationMain(): 앱의 본체에 해당하는 객체인 UIApplication 객체 생성
4. UIApplication 객체: Info.plist 파일을 바탕으로 앱에 필요한 데이터와 객체 로드
5. AppDelegate 객체 생성 및 UIApplication 객체와 연결
6. 이벤트 루프 생성 등 실행에 필요한 준비 진행
7. 실행 완료를 앞두고 UIApplication객체가 AppDelegate에 `application(_:didFinishLaunchingWithOptions:)` 메시지 전송


### 스위프트에서 AppDelegate클래스 지정
스위프트는 C기반 언어가 아니므로, 스위프트 기반 프로젝트에는 main.m파일이 없고 엔트리포인트역시 존재하지 않는다. 대신 스위프트는 위의 1~5 과정을 어노테이션으로 대체한다.
```swift
//  AppDelegate.swift

import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        return true
    }
```
- 스위프트에서는 직접 UIApplicationMain()을 호출하여 델리게이트 클래스를 인자값으로 전달할 수 없다.
- 그 대신 앱 델리게이트 역할을 할 클래스에 `@UIApplicationMain`어노테이션을 걸어 표시하는 방식으로 시스템에 델리게이트 클래스 정보 전달. iOS시스템은 앱을 실행할 때 이 어노테이션이 표시된 클래스를 찾아 델리게이트로 지정한다.

### 간단한 iOS앱의 생명주기
<img src="/Users/jeonmijin/Documents/iOS/level3/swift-vendingmachineapp/Screenshot/step1-2.png" width="20%">
(왼쪽: iOS시스템 프레임워크 / 오른쪽: 커스텀코드)

- main함수 실행과 UIApplicationMain()호출
- 스위프트에서는 `@UIAnnotationMain`찾아서 실행
- 커스텀코드측(AppDelegate에 작성된 `application()`메소드가 시스템에 의해 자동호출
  - AppDelegate클래스에 `application()` 메소드에 원하는 코드를 작성해두면 앱이 처음 시작될때 해당 코드를 실행할 수 있다.
- 시스템 프레임워크의 이벤트 루프 실행되면서 이벤트 핸들에 의해 커스텀 코드로 연결됨
  - 커스텀 코드중 `@IBAction`으로 구현된 코드가 이벤트를 받아와서 메소드 내에 구현된 코드를 실행하므로 이벤트 핸들이라고 할 수 있다. **`@IBAction`메소드같이 사용자가 어떤 이벤트를 보냈을때 실행되도록 구현해놓은 메소드를 이벤트 핸들이라고 할 수 있다.**
  - (원하는 이벤트가 발생했을때 제어하도록 커스텀코드와 연결해놓으면 이벤트 루프에서는 특정 이벤트가 발생했을때 우리가 만든 핸들을 통해(`@IBAction`) 커스텀코드를 실행할 수 있도록 처리함)
- AppDelegate클래스의 `applicationWillTerminate()`호출하여 앱 메모리에서 제거하기 위한 준비


## The Main Run Loop
- 앱의 main run loop은 사용자 관련 프로세스를 받은 순서대로 처리한다.(터치이벤트와 같은)
- UIApplication객체는 앱이 launching되는 시점에 main run loop을 생성한 뒤 run loop로 이벤트를 처리
- main run loop은 앱의 메인 스레드에서 동작

### 사용자의 이벤트가 인식되는 구조
1. 사용자는 디바이스에서 특정 액션을 취함 (터치, 줌 등)
2. 그 액션에 해당하는 이벤트가 시스템에 의해 생성, UIKit에서 생성한 port를 통해 앱에 전달
3. 이벤트들은 앱 내부적으로 queue에 저장(FIFO)
4. UIApplication객체가 가장 먼저 이 이벤트를 받아서 어떤 동작이 취해질 지 결정
  - 터치 이벤트의 경우 main window객체가 인식하고 window객체가 다시 터치가 발생한 view로 이벤트를 전달함
  - 다른 이벤트들도 다양한 app객체에 따라 조금씩 다르게 동작

### 주로 발생되는 이벤트 처리에 대한 설명
- Touch이벤트: **터치 이벤트가 발생한 view객체** 로 전달
  - view는 응답을 할 줄 아는(Responder) 객체이므로 터치 이벤트가 발생한 뷰 객체로 전달됨. 만약 해당 뷰에서 처리되지 않는 터치이벤트는 Responder chain을 따라 계속 내려가게됨
- Remote control / Shake motion events: **First responder object객체**
  - 미디어콘텐츠의 재생이나 되감기 등과같은 remote control이벤트는 주로 헤드폰같은 악세사리에서 발생
- Accelerometer / Magnetometer / Gyroscope: **내가 정한 객체** 로 전달
  - 가속도계, 방향, 중력센서(자이로스코프)와 관련된 이벤트를 내가 지정한 객체로 전달됨
- Location: **내가 정한 객체**
  - Core location Framework를 사용해서 위치와 관련된 이벤트 등록
- Redraw: **업데이트가 필요한 객체**
  - Redraw 이벤트는 이벤트 객체를 갖지는 않고, 단순히 업데이트가 필요한 view객체에 요청
- **참고**, 이벤트 소스 종류
  - 입력소스(input source): 다른 thread나 어플리케이션에서 전달되는 메시지 이벤트(비동기식)
  - 타이머소스(timer source): 예정시간이나 반복수행간격에 따라 발생하는 이벤트(동기식)
- **Touch나 Remote Control** 같은 이벤트는 앱의 Responder객체를 통해 처리된다.
- Responder객체는 앱의 모든 곳에 존재
- UIApplication, 커스텀 view객체, ViewController객체 모두 Responder객체에 해당된다.
- 대부분의 이벤트는 특정 객체를 대상으로 전달되지만 다른 객체로 변경할 수도 있음
  - UIView의 hitTest나 pointInside사용 등
  - 이벤트를 받은 객체에서 해당 이벤트를 처리하지 않는다면 부모뷰로 이벤트를 전달 시킬 수 있다.
- **Control** 에서 발생하는 이벤트(ex. Button)는 보통 몇 가지로 제한된 이벤트만 존재한다. 그 이벤트들을 다시 Action메시지로 패키징하여 지정된 객체로 전달한다. (Target-action design pattern)

## 앱의 실행상태
<img src="./Screenshot/step3-1.png" width="100%">

시스템 내에서 일어나는 액션을 통해 시스템은 앱의 상태를 바꾸게 됨.
- Not running: 아직 실행되지 않거나 실행되다가 시스템에 의해 종료
- Inactive: 앱이 foreground에 올라와있지만 이벤트를 받지않고있는 상태(다른 코드를 실행 중일 수 있다) 앱에서는 보통 앱 상태변화가 일어나는 동안에 짧게 이 상태를 갖게됨
- Active: 앱이 foreground에서 실행중이고 이벤트를 받고 있는 상태
  - *(Inactive와 Active 상태를 합쳐서 Foreground 라고 함)*
- Background:
  - 앱이 background에 있고 코드를 실행하고있는상태.
  - 대부분의 앱은 suspended로 가는 도중에 잠깐 이 상태에 머무름.
  - 이외에도 background상태로 실행되는 앱의 경우에는 Inactive대신에 Background상태로 진입한다.
- Suspended:
  - 앱이 background에 있으면서 코드를 실행하고있지 않은 상태.
  - 시스템은 자동으로 suspended로 변경하고 따로 알려주지 않는다.
  - suspended상태에로 메모리에는 올라가 있음.
  - 메모리가 부족한 상황이 오면 시스템이 앱을 kill하기도 하는데, 이 때도 따로 알려주지 않는다.

### AppDelegate methods
> - 대부분의 상태변화는 AppDelegate객체의 메소드 호출을 거친다.
> - AppDelegate.swift 파일에는 앱의 상태에 따라 실행되는 함수들이 정의되어 있다.
> - 앱의 상태에 따라 실행되는 delegate 함수들이 정의되어 있기때문에 함수안에 코드를 작성 함으로써 앱의 특정 상태에서 동작하는 로직을 구현 할 수 있다.

- `application:willFinishLaunchingWithOptions:` 앱을 실행할 때 최초로 실행할 코드를 작성하면 좋음.
- `application:didFinishLaunchingWithOptions:` 앱의 화면이 사용자에게 보여지기 직전에 최종 초기화 작업을 진행
- `applicationDidBecomeActive:` 앱이 이제 Foreground로 갈 것. 최종 준비작업.
- `applicationWillResignActive:` 앱이 Foreground에서 다른 상태로 전환이 될것임을 알려줍니다. 앱이 잠잠한(quiescent) 상태로 변환되는 작업을 여기서 진행하세요.
- `applicationDidEnterBackground:` 앱이 Background로 돌아감. 그리고 언제든지 Suspended상태로 변환이 될 수 있다.
- `applicationWillEnterForeground:` 앱이 Background에서 다시 Foreground로 돌아오게 될 것임을 알려줌. 아직 앱이 Active상태는 아님.
- `applicationWillTerminate:` 앱이 종료될 것임을 알려줍니다. 만약 앱이 Suspended상태라면 이 메소드는 호출되지 않습니다.

```
application(_:didFinishLaunching:) - 앱이 처음 시작될 때 실행
applicationWillResignActive: - 앱이 active 에서 inactive로 이동될 때 실행
applicationDidEnterBackground: - 앱이 background 상태일 때 실행
applicationWillEnterForeground: - 앱이 background에서 foreground로 이동 될때 실행 (아직 foreground에서 실행중이진 않음)
applicationDidBecomeActive: - 앱이 active상태가 되어 실행 중일 때
applicationWillTerminate: - 앱이 종료될 때 실행
```
[참고-앱 생명주기(App Lifecycle) vs 뷰 컨트롤러 생명주기(ViewController Lifecycle) in iOS](https://medium.com/ios-development-with-swift/%EC%95%B1-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-app-lifecycle-vs-%EB%B7%B0-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-view-lifecycle-in-ios-336ae00d1855)
[참고-앱라이프사이클](http://rhammer.tistory.com/94)
<img src="./Screenshot/step3-2.png" width="100%">


## 아카이빙
- iOS앱은 기본적으로 사용자에게 데이터를 조작할 인터페이스를 제공함
- 모델 객체: 사용자가 조작하는 데이터를 보관하는 역할 (저장과 로딩)
- 뷰 객체: 단순히 그 데이터 반영
- 컨트롤러 객체: 뷰와 모델 객체의 동기화 책임
- **아카이빙은 iOS에서 모델 객체를 저장하는 가장 흔한 방법 중 하나임**
  - **아카이빙:** 객체의 아카이빙이란 그 객체의 프로퍼티를 모두 기록하고 파일 시스템에 그 내요을 저장한다는 뜻
  - **언아카이빙:** 아카이브한 데이터로부터 객체를 다시 만든다.
- 아카이빙/언아카이빙 할 클래스들은 반드시 NSCoding프로토콜을 따라야한다.
- 스토리같은 인터페이스 파일에 객체를 추가할때 객체들은 아카이빙 된다.
- 실행 중에 객체들은 인터페이스 파일에서 언아카이빙되어 메모리에 로드된다.
- UIView와 UIVIewController들은 모두 NSCoding 프로토콜을 따른다.
- 아카이빙하고싶은 모델객체를 NSCoding을 따르게 만들어준다.

```swift
// NSCoding구현 필수 메서드
class Robot: NSCoding {
  var name : String = ""
  var nemesis : Robot
  var model : Int

  func encode(with aCoder: NSCoder) {
    aCoder.encode(name, forKey: "name")
    aCoder.encode(nemesis, forKey: "nemesis")
    aCoder.encode(model, forKey: "model")
  }
  // 메시지를 받으면 인자로 전달된 NSCoder객체 안의 모든 프로퍼티들을 인코딩한다. 데이터 스트림은 키-값 쌍으로 구성되어 파일시스템에 저장된다.

  required init?(coder aDecoder: NSCoder) {
    name = aDecoder.decodeObject(forKey: "name") as! String
    nemesis = aDecoder.decodeObject(forKey: "nemesis") as! Robot?
    model = aDecoder.decodeInteger(forKey: “model")
  }
}
```
- `aCoder.encode(저장할 객체, forKey: "인코딩 프로퍼티를 식별할 키값이 될 문자열")`
- 아카이브에서 로드되는 객체들은 `init(coder:)`메시지를 받는다. 이 메서드는 `encode(with:)`에서 인코딩된 모든 객체를 꺼내와 해당 프로퍼티네 할당해야한다.

```swift
func applicationDidEnterBackground(_ application: UIApplication) {
      UserDefaults.standard.set(NSKeyedArchiver.archivedData(withRootObject: self.vending), forKey: "vendingMachine")
  }
```

### ObjectIdentifier 인코딩
- ObjectIdentifier는 아카이빙되지 않는다. 따라서 ObjectIdentifier를 유추할 수 있는 형태로 인코딩하고, 인코딩된 값에서 ObjectIdentifier를 구해서 디코딩한다.
- Stock 클래스의 속성 `inventory`형태가 `[ObjectIdentifier: [Beverage]]`이기때문에 Beverage로부터 ObjectIdentifier를 유추해내는 로직이 필요했다.
  - encode 메소드 내에서 속성을 그대로 저장하는것이 아니라 로컬변수 저장이 가능하다.
```Swift
// Stock.swift

class Stock: NSObject, NSCoding {
    func encode(with aCoder: NSCoder) {
        var items = [[Beverage]]()
        for set in self.inventory {
            items.append(set.value)
        }
        aCoder.encode(items, forKey: "items")
    }

    required init?(coder aDecoder: NSCoder) {
        guard let tempItems = aDecoder.decodeObject(forKey: "items") as? [[Beverage]] else {
            return
        }
        let flattenItems = tempItems.joined()
        let itemSets = flattenItems.reduce(into: [ObjectIdentifier: [Beverage]]()) {
            $0[ObjectIdentifier(type(of: $1)), default:[]].append($1)
        }
           self.inventory = itemSets
    }

    private var inventory = [ObjectIdentifier: [Beverage]]()
```
