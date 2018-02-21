---
layout: post
title: 알고리즘 - 퀵소트 (Quick Sort)
tags: Algorithm sort 알고리즘 swift
categories: Study
---

# 퀵소트 알고리즘 (Quick sort Algorithm) 기초
 1. 파티션에 원소가 하나면 종료.
 2. 적당한 숫자 하나를 pivot으로 고른다.
 3. pivot보다 작은 숫자는 왼쪽 큰 숫자는 오른쪽으로, pivot양 옆으로 하위배열을 나눔
 4. pivot의 왼쪽 배열과 오른쪽 배열에 대해 반복적으로 수행

***



 - 평균적으로 빠른 알고리즘이지만 O(n log n)
 - 최악의 경우(배열을 절반으로 나누지 않고 가장 처음이나 끝 수를 pivot으로 고른경우) O(n^2). 느리다.

## 예시
### 최악의 경우
- 배열이 [1,2,3,4,5,6,7,8]이고 pivot은 항상 첫번째 value 일때
- 두 개의 하위배열은 항상 빈 배열이 되고, 호출스택의 전체 높이는 8, 즉 O(n).
- 각각의 호출 스택을 분석하는데 걸리는 시간은 O(n)
- **전체 시간 복잡도는 O(n) * O(n) = O(n^2)**

### 평균적인 경우
- 배열이 [1,2,3,4,5,6,7,8]이고 pivot은 항상 정 가운데 있는 value를 선택
- 호출스택의 전체 높이는 4, 즉 O(log n)
- 각각의 호출 스택을 분석하는데 걸리는 시간은 O(n)
- **전체 시간 복잡도는 O(log n) * O(n) = O(n log n)**

## 예시 코드

1. pivot이 배열의 첫번째 value가 되는 경우 (최악의 경우)

```Swift
func quickSort(_ arr: [Int]) -> [Int] {
    let pivot = arr[0]
    var equal = [Int]()
    var less = [Int]()
    var greater = [Int]()

    if arr.count < 2 {
        return arr
    } else {
    for i in arr {
        if i < pivot {
            less.append(i)
        }
        if i == pivot {
            equal.append(i)
        }
        if i > pivot {
            greater.append(i)
        }
    }
    return quickSort(less) as [Int] + [pivot] + quickSort(greater) as [Int]
    }
}

quickSort([5,3,4,9,10])
```

2. pivot이 배열의 중간위치에 있는 value인 경우(평균적인 경우)

```Swift
func quick(_ a: [Int]) -> [Int] {
    guard a.count > 2 else { return a }

    let pivot = a[0]
    let less = a.filter { $0 < pivot }
    let equal = a.filter { $0 == pivot }
    let greater = a.filter { $0 > pivot }

    return quick(less) + equal + quick(greater)
}
```
