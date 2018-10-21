---
layout: post
title: NotificationQueue 클래스
tags: iOS NotificationQueue NotificationCenter
categories: iOS
---
> [노티피케이션이 시리얼큐에서 처리가되는건가!?](https://jinios.github.io/til/2018/10/08/TIL/)라는 궁금증에 찾아본 후 간단한 정리

`class NotificationQueue : NSObject`

- Notification들은 NotificationQueue에 위치함 (FIFO)
- NotificationCenter에 의해 포스팅되는 notification들은 동기적으로 전달된다.
- 즉, 앞선(현재의)노티가 run loop을 빠져나가거나 run loop이 한가해지기전까지 딜레이 될 수 있다.
- 중복되는 노티들은 여러 노티가 포스팅되더라도 합쳐질 수 있다. (하나의 노티만 보내기 위해)
- 모든 스레드는 기본 노티피케이션 큐를 가지고있으며, 그것은 default notification center(`NotificationCenter.default`)와 연관되어있다.
- 커스텀 노티피케이션 큐를 만들어서 사용할 수 있으며 센터와 스레드마다 여러 큐를 가질 수 있다.
- 노티피케이션이 동기적으로 동작한다는것은 앱이 바쁜 상태이거나 여러 노티다 반복될때 앱을 느리게 만들거나 멈추게 만들 수 있는 문제의 소지가 있다.
- 이때 NotificationQueue만들어서 사용하면 비동기적으로 동작시킬 수 있고, 이런 문제를 완화할 수 있다.
- 예시
  ```swift
  let notification = Notification(name: Notification.Name("MyValueChanged"))
  NotificationQueue.default.enqueue(notification, postingStyle: .whenIdle, coalesceMask: .none, forModes: nil)
  ```

#### 출처
- [애플 개발자문서](https://developer.apple.com/documentation/foundation/notificationqueue)
- [Hacking with Swift](https://www.hackingwithswift.com/example-code/system/how-to-send-notifications-asynchronously-using-notificationqueue)
