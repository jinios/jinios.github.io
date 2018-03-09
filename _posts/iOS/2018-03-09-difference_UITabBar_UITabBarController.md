---
layout: post
title: UITabBarController & UITabBar의 차이
tags: iOS UITabBarController UITabBar
categories: iOS
---

### UITabBarController & UITabBar
일반적인 관점에서의 View과 Controller에 대한 설명은 아래와 같다.
- View : 사용자의 입력을 받고 사용자에게 보여주는 데이터를 어떻게 보여줘야하는지 관리하는 객체
- Controller : 사용자의 입력을 어떻게 처리해야 하는지 알고 사용자에게 어떤 데이터를 보여줘야하는지 관리하는 객체
<br/>

이런 관점으로 접근한다면,
- UITabBar는 **View에 가까운 개념으로, UITabBar는 사용자에게 탭을 어떻게 보여줘야하는지에대한 책임이 있는 객체**
- UITabBarController는 **Controller에 가까운 개념으로, 사용자가 특정한 탭을 선택했을때 어떤 탭이 보여져야하는지에대한 책임이 있는 객체라고 생각한다.** 고 생각한다. 이런 관점에서 보면 UITabBarController와 UITabBar는 **상/하위 모듈 관계** 라고 볼 수도 있다고 생각한다.

다만, 보통 UITabBar는 UITabBarController와 결합해서 쓰이지만, UITabBar 단일하게 자체만으로도 쓰일 수도 있기때문에 아래와 같은 특징이 공통점이라고 말할 수 있다. (TabBar에 대한 일반적인 설명)
- 사용자가 뷰 컨트롤러들 간에 전환할 수 있는 방법을 제공함
- 특정 탭을 누르면, 그 탭에 연결된 뷰 컨트롤러의 뷰가 나타난다.

[참고링크](https://stackoverflow.com/questions/2971181/what-is-the-difference-between-uitabbar-and-uitabbarcontroller)
