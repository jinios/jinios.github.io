---
layout: post
title: Concurrent Programming강의 정리
tags: GCD thread deadlock ConcurrentProgramming
categories: iOS
---
## Concurrent Programming
> - JK강의 정리
> - 운영체제 관련된 공부를 하면 이해가 더 잘된다...고합니다.

### 과거
> `운영체제 OS - Process` 1:1 구조

- 작업단위 일을 한순간에 한가지만 할 수 있는가?
- 만약 하나의 작업이 순차적으로 일어난다면 모든 일을 처리하는 것은 결국 시간 싸움일 수 있음

### 과도기
> 멀티프로세스가 가능한 운영체제구조, 1:n 구조, 시간을 계속 나눠서 실행

- 프로세스가 많아질수록 해당 일에 할당해줄수있는 시간이 줄어듦
- OS입장에서도 프로세스를 처리하는 비용이 부담스러움
  - 우선순위를 저장하고 작업을 할당, 처리하는 과정또한 큰 작업이 됨
- 맥락(context)이 변경될때(switch) 컴퓨터도 마찬가지로 비용이 많이 발생한다.
  - 프로세스 변경은 컴퓨터에게도 적지 않은 부담을 주는 일임

### GUI가 적용되어있는 프로세스들의 등장
- 화면을 처리해야하기 시작함
- 마우스나 키보드의 이벤트를 처리해야하는 작업이 생김 (입,출력)
- 작업을 나눠서 수행해야하는 일이 많아지다보니 더 작은 단위로 일을 처리하는 방법 고민

### Thread
- 운영체제가 프로세스 내에서도 작업을 logical하게 처리할 수 있도록 더 작은 단위인 thread만듦
- thread도 운영체제의 지원이 있어야 가능함
- thread는 프로세스 내에서 동시처리(하나의 프로세스는 시간을 나눠서 실행되므로)
- GUI관련 로직은 메인스레드에서 처리
- 많은 worker들이 엘리베이터를 이용해서 많은 짐을 옮겨야할때를 비유했을때,
  - elevator: CPU
  - worker: thread
  - load: task
- 엘리베이터가 크면 한꺼번에 많은 worker가 동시에 짐을 옮길 수 있음
- 많은 수의 엘리베이터(cpu- 멀티코어?)

> Thread로 주제가 옮겨지면서 하나의 앱(process)안에서 여러 스레드가 몇개까지 있을 수 있을까?로 화두가 전환됨.

- Thread가 무작정 많다고해서 무조건 효율이 좋아지진 않음
- 정확하게 가장 효율적인 thread 개수와 구조는 OS가 알고있음
- 메모리의 한계때문에도 thread를 무한정 늘릴 수 없음
- 또한 CPU의 상황에따라서 thread를 어떻게 효율적으로 운영할지는 개발자가 결정해야하는 면이 있었음(과거에)
- Objective-C의 아래와 같은 메소드로 처리할 수 있었다.
```
- performSelectorOnMainThread:withObject:waitUntilDone:
- performSelector:onThread:withObject:waitUntilDone:
- performSelector:withObject:afterDelay:
- performSelectorToBackground:withObject:
```
- 장점: 화면이 멈추지 않고 다른 thread에서 로직 처리를 할 수 있다.
- 단점: Thread간의 racing문제로 인해서 다른 동작을 할 수 있는데 그걸 디버깅하기가 어렵다.

### 현재
- 점점 CPU코어 수를 늘려가면서 병렬처리가 좋아지도록 하드웨어가 발전함.
- 따라서 병렬처리를 해서 가능한 범위까지 CPU를 모두 사용해야 앱 성능이 좋아지는 방향으로 가는 추세.
- 개발자가 직접 하는것 보다 운영체제가 스레드를 처리하고 관리하는게 더 효율적임(Only the OS can balance unrelated subsystems)

### GCD등장
- 코드조각을 queue에 넣어서 스케쥴링 하는 방식으로 바뀜
- 코드묶음을 클로저/블럭 단위로 queue에 넣어놓으면 그 queue에 할당되어있는 thread들을 찾아서 queue에서 코드를 가져와서 실행.
- 큐 종류를 선택해서 관리(global queue, serial queue)
- 메인 스레드는 지정할 수 있음
- 다른 큐에다 넣으면 어느 thread에서 돌아갈지 모름. 특정한 thread가 아니어도 되는 경우에는 dispatch queue에 블럭을 만들어주면 운영체제에서 최적화된(쉬고있는)스레드를 찾아서 실행시켜줌
- 스레드를 지정해서 써야하면 스레드를 만들어서 실행하면 됨!
- 스레드를 만들었으면 스레드당 autoreleasepool을 만드는게 핵심
  - 저 scope안에서만들어진 객체들은 scope가끝나면 해제하고싶을때 autoreleasepool을 사용한다.

```swift
class SomeClass {
 var thread : Thread? = nil

 func doTimeConsumingOperation(operation : Any?) {
   thread = Thread(target: self,
      selector: #selector(SomeClass.runHelper), object: operation)
      thread?.start()
 }

 @objc func runHelper(operation : Any?) {
   autoreleasepool { () in
     //operation.doOperation()
     print("Other thread is running...")
   }
 }
}
let some = SomeClass()
some.doTimeConsumingOperation(operation: nil)
```
- 같은 큐에만 넣는다면 동시에 실행되서 충돌될일이없음(locking)이 필요없다.

### 스레드 -> 프로세스 -> 클로저
- 병렬프로그래밍은 **스레드 -> 프로세스 -> 클로저** 의 흐름으로 발전
- 클로저만 가지고 어디서 실행할지 결정해서 넣어주면 되는 방식으로 발전함 (작업단위가 작아지고 유연해짐)

### Deadlock
- 데드락: 두 개 이상의 작업이 서로 상대방의 작업이 끝나기 만을 기다리고 있기 때문에 결과적으로 아무것도 완료되지 못하는 상태
- **스토어앱에서 네트워크에서 데이터를 받아오는 비동기 작업을 처리하다가 [데드락 상황](https://github.com/jinios/swift-storeapp#1-%EB%8D%B0%EB%93%9C%EB%9D%BDdeadlock)을 발견할 수 있었다.**
   - 이 정의와 설명을 강의로만 들었을때는 이해가 잘 되지 않았는데, 한번 상황을 맞닥뜨리고나니 이해가 됐다.

### queue의 종류
```
Concurrent queue - global queue. 넣으면 여러가지가 한번에 실행될 수 있다.
Serial queue - 한번에 하나만 실행된다.
global queue는 serial queue가 아님!을 유의하고 써야함
```

### dispatchqueue / operationqueue
- queue에 들어가면 실행해야만 없어지므로 취소할 수 없다.
- 따로 취소하고싶으면 dispatchqueue가 아니라 operation을 만들어서 operationqueue를써야함.
- 캔슬이 중요하면 operation queue를 사용.
