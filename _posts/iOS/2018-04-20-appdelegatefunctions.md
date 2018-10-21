---
layout: post
title: AppDelegate methods by App lifecycle
tags: iOS appDelegate
categories: iOS
---
> 앱 라이프사이클에 따른 AppDelegate methods 정리
> Strategies for Handling App State Transitions 참고, 간단번역과 정리

> - 대부분의 상태변화는 AppDelegate객체의 메소드 호출을 거친다.
> - AppDelegate.swift 파일에는 앱의 상태에 따라 실행되는 함수들이 정의되어 있다.
> - 앱의 상태에 따라 실행되는 delegate 함수들이 정의되어 있기때문에 함수안에 코드를 작성 함으로써 앱의 특정 상태에서 동작하는 로직을 구현 할 수 있다.

```
application(_:didFinishLaunching:) - 앱이 처음 시작될 때 실행
applicationWillResignActive: - 앱이 active 에서 inactive로 이동될 때 실행(foreground)
applicationDidEnterBackground: - 앱이 background 상태일 때 실행
applicationWillEnterForeground: - 앱이 background에서 foreground로 이동 될때 실행 (아직 foreground에서 실행중이진 않음)
applicationDidBecomeActive: - 앱이 active상태가 되어 실행 중(foreground)
applicationWillTerminate: - 앱이 종료될 때 실행
```

- `application:willFinishLaunchingWithOptions:`
  - 앱을 실행할 때 최초로 실행할 코드를 작성하면 좋음.
  - 앱을 실행할 때 필요한, 앱 번들이나 iCloud 컨테이너에 저장된 데이터 불러와서 설정할때 사용

- `application:didFinishLaunchingWithOptions:`
- 앱의 화면이 사용자에게 보여지기 직전에 최종 초기화 작업을 진행
- `applicationDidBecomeActive:` 앱이 이제 Foreground로 갈 것. 최종 준비작업.
- `applicationWillResignActive:`
  - 앱이 Foreground에서 다른 상태로 전환이 될것임을 알려줍니다. 앱이 잠잠한(quiescent) 상태로 변환되는 작업을 여기서 진행하세요.
  - Alert-based interruptions으로 인해 앱의 컨트롤이 일시적으로 중단될 수 있다. 앱은 foreground상태에서 계속 동작하지만, touch event를 받을 수 없는 상태이다. (It does continue to receive notifications and other types of events, such as accelerometer events, though.)
  - 이러한 상태에 대처하기위해 `applicationWillResignActive`를 구현해야함
    - Save data and any relevant state information.
    - Stop timers and other periodic tasks.
    - Stop any running metadata queries.
    - Do not initiate any new tasks.
    - Pause movie playback (except when playing back over AirPlay).
    - Enter into a pause state if your app is a game.
    - Throttle back OpenGL ES frame rates.
    - Suspend any dispatch queues or operation queues executing non-critical code. - (You can continue processing network requests and other time-sensitive background tasks while inactive.)
- `applicationWillEnterForeground`:
  - 앱이 Background에서 다시 Foreground로 돌아오게 될 것임을 알려줌. 아직 앱이 Active상태는 아님.
  - 이 함수는 `applicationWillResignActive:`는 반대의 상태일때 호출되는데, 이때는 reactivation에 필요한 동작을 구현해야한다.
    - restart timers, resume dispatch queues, and throttle up OpenGL ES frame rates again. However, games should not resume automatically; they should remain paused until the user chooses to resume them.

- `applicationDidEnterBackground:` 앱이 Background로 돌아감. 그리고 언제든지 Suspended상태로 변환이 될 수 있다.
- `applicationWillTerminate:` 앱이 종료될 것임을 알려줌. 만약 앱이 Suspended상태라면 이 메소드는 호출되지 않는다.


### Responding to Temporary Interruptions
- **Notifications** that display a banner do not deactivate your app in the way that alert-based notifications do. Instead, the banner is laid along the top edge of your app window and your app continues receive touch events as before. However, if the user pulls down the banner to reveal the notification center, your app moves to the inactive state just as if an alert-based interruption had occurred. Your app remains in the inactive state until the user dismisses the notification center or launches another app. At this point, your app moves to the appropriate active or background state. The user can use the Settings app to configure which notifications display a banner and which display an alert.

- **Pressing the Sleep/Wake button** is another type of interruption that causes your app to be deactivated temporarily. When the user presses this button, the system disables touch events, moves the app to the background, sets the value of the app’s applicationState property to UIApplicationStateBackground, and locks the screen. A locked screen has additional consequences for apps that use data protection to encrypt files.

### What to Do When Your App Enters the Foreground
> Note: The UIApplicationWillEnterForegroundNotification notification is also available for tracking when your app reenters the foreground. Objects in your app can use the default notification center to register for this notification.


- [참고 - App Programming Guide for iOS](https://developer.apple.com/library/archive/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/StrategiesforHandlingAppStateTransitions/StrategiesforHandlingAppStateTransitions.html#//apple_ref/doc/uid/TP40007072-CH8-SW2)
- [참고 - 앱 생명주기(App Lifecycle) vs 뷰 컨트롤러 생명주기(ViewController Lifecycle) in iOS](https://medium.com/ios-development-with-swift/%EC%95%B1-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-app-lifecycle-vs-%EB%B7%B0-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-view-lifecycle-in-ios-336ae00d1855)
- [참고 - 앱라이프사이클](http://rhammer.tistory.com/94)
