---
layout: post
title: AppDelegate의 콜백함수에서 동일한 동작 구현하기
tags: iOS
categories: iOS
---
> AppDelegate의 콜백함수에서 즐겨찾기 데이터를 저장하거나 가져올때의 의문점. 콜백 함수의 순서에따라서 연속적으로 동작하는데 그 안에서 똑같이 데이터를 저장해도 괜찮을까? 비효율적이지 않을까?

- 즐겨찾이 가져오기위해 구현한 함수
```Swift
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // loadedData가 없는경우 favoriteList를 저장해주지 않고 그냥 넘어가면 하위 모듈들에서 접근하는 FavoriteList.shared()로 받는 인스턴스는 모두 그냥 Set<Int>()로 init이 되니까 상관없음
        guard let loadedData = DataStorage<FavoriteList>.load() else { return true }
        FavoriteList.loadSavedData(loadedData) // Favorite리스트를 저장했던 인스턴스로 대체해줌

        return true
    }
```


- 첫 시작
```
application(_:didFinishLaunchingWithOptions:)
applicationDidBecomeActive
```

- 홈버튼눌러서 홈 화면으로 갈때
```
applicationWillResignActive
applicationDidEnterBackground
```

- 실행(백그라운드였다가 그대로 다시뜰때)
```
applicationWillEnterForeground
applicationDidBecomeActive
```
- 홈버튼 두번 연속 눌렀을 시 (앱 화면은 보이지만 inactive상태)
```
applicationWillResignActive
```

- 홈버튼 두번 눌러서 위로 스와이프하여 끌때 호출순서 테스트
```
applicationWillResignActive
applicationDidEnterBackground
applicationWillTerminate
```

- 테스트시, 백그라운드 상대로 갈때 항상 호출되는 함수는 두개
```
applicationWillResignActive
applicationDidEnterBackground // 오랫동안 active상태가아닐때
```
- 예비상태와 / 진짜 상태로 들어가는게 꼭 두개씩 호출되는데 어떻게 구분하고 함수를 구현해야할까?
- 모두 구현하는게 제일 좋지만, 똑같은 동작을 반복하게 하기 보다는 해당 콜백들이 불리는 시간차동안 내가 저장할 즐겨찾기 데이터가 변경이 됐는지 flag를 두고 flag에 따라서 동작하게 하는게 좀 더 나은 방법
- 앱델리게이트 콜백함수 중 resignActive나 applicationWillTerminate같은 함수에서 비동기작업을 하면 해당 작업이 끝날떄까지는 유지된다고 한다. 보통은 1분내외로 알려져있지만 시간이 더 필요하다면 await처리를 해야 할 것
