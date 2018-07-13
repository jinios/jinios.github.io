---
layout: post
title: Handling UIKit Gestures 번역
tags: iOS GestureRecognizer
categories: iOS
---
> 혼자 공부&정리용으로 애플 개발자 문서 Handling UIKit Gestures Article 번역

[참고링크](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/handling_uikit_gestures)

## Handling UIKit Gestures
GestureRecognizer를 사용하여 터치를 간단하게 핸들링하고 일관적인 사용자 경험(user experience를 만들 수 있다.

## Overview
- GestureRecognizer는 터치나 프레스 이벤트를 처리하는 가장 간단한 방법이다.
- 하나 이상의 GestureRecognizer를 어떤 뷰에도 붙일 수 있다.
- GestureRecognizer는 진행과 이벤트 해석에 필요한 모든 로직을 **캡슐화하고** 패턴(이벤트 종류)에 일치시킨다. (match them to a known pattern. )
- 맞는 이벤트가 감지되면, GestureRecognizer는 타겟 객체에 노티를 보낸다.
  - (Gesture recognizer notifying its target)
- 위에서 말하는 타겟객체는 viewController일수도, view일수도, 혹은 앱 내의 다른 객체일수도있다.
- **Action**: 해당 제스쳐가 감지됐을때 호출될 메서드
- **Target**: 제스쳐를 처리할 타겟 객체


## action methods
- GestureRecognizer는 target-action 디자인패턴을 사용하여 noti를 주고받는다.
- 예를들어 UITapGestureRecognizer가 적용된 뷰에서 single-finger tap이 감지되면, 액션 메소드가 호출된다.
- GestureRecognizer의 인스턴스를 생성할때 액션메서드를 인자로 넘겨줌
  - ex) `let panGesture = UIPanGestureRecognizer(target: self, action: #selector(cardDragged(_:)))`
  - 액션메서드: 해당 제스쳐가 감지됐을때 호출될 메서드
- 액션메서드는 UIGestureRecognizer sender객체를 인자로 전달받는다.
- 해당 sender객체로 제스쳐에 대한 데이터를 얻어올 수 있다.


## State property
- GestureRecognizer의 액션메소드는 제스쳐의 상태에 따라 다른 동작을 하도록 구현할 수 있다.
- Continuous Gesture
  - .began
  - .changed
  - .ended
  - .cancelled
