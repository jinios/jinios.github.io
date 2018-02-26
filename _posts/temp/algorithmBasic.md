##재귀(Recursive)

#### 무한루프에 빠지지 않으려면
- 기본단계(Base Case): 적어도 하나의 recursion에 빠지지 않는 경우가 존재해야한다. (적어도 하나의 자기자신을 호출하지 않고 종료되는 경우가 있어야 한다.)
- 재귀단계(Recursive case): recursion을 반복하다 보면 결국 Base case로 수렴해야한다.

재귀로 팩토리얼 구현
0! = 1
n! = n*(n-1)!

재귀로 x^n^ 구현
x^0^ = 1
x^n^ = x * x^n-1^ (if n>0)
(근데 결국 이렇게(x * x^n-1^) 나눈 것도 분할정복에서 가장 기본단계를 찾는 것과 동일)


```swift
// [source](https://www.hackerrank.com/challenges/kangaroo/problem)
import UIKit

let input = [21,6,47,3]
let positionA = input[0]
let positionB = input[2]
let distanceA = input[1]
let distanceB = input[3]

var currentPositionA = positionA
var currentPositionB = positionB
var flag = 0

var A = [positionA]
var B = [positionB]
var dif = [Int]()

/*
 한 단계(자기 distance대로) 전진할때마다의 차이를 구한다.
 1. "YES"인 경우 : 차이가 0이 되는 turn에 break하고 YES출력

 2. "NO"인 경우 1,2 :
    <1>
    YES인 경우를 저렇게 알아낼 수 있는 반면에,
    "NO"인 경우에는 반복문을 break하는 turn을 찾을 수 없다.(0이 되는 타이밍이 없음)
    대신에 두 캥거루의 거리 차이 배열의 값들 중에서 처음으로 음수가 됐을때의 index의 바로 앞 (index-1)을 검사한다.
    해당 (index-1)의 값이 0이 아니면 "NO"인 경우로, 반복문을 break할 수 있다.
    (두 캥거루의 거이 차이가 음수로 넘어갔다는 것은 단계가 진행될수록 그 차이가 더 벌어지기만 할 것이기 때문)

    <2>
    첫번째의 "NO"인 경우는 단계를 진행하면서 캥거루A가 앞질러가면서
    B가 이동하는 포지션과 만나지 못하는 경우고, (단계가 진행될수록 positionA > positionB)
    두번째 "NO"는 캥거루B가 계속해서 A보다 앞서있는 경우다. (단계가 진행될수록 positionA < positionB)
    이런경우엔 둘 사이의 거리가 계속해서 증가한다. 이미 프로그램이 가지고있는 dif 배열을 가지고 검사한다.
 */

// while로 변경해야함
while true {
    currentPositionA += distanceA
    currentPositionB += distanceB
    A.append(currentPositionA)
    B.append(currentPositionB)
    dif.append(currentPositionB-currentPositionA)
    guard dif.last! != 0 else {
        print("YES")
        break
    }
    if let i = dif.index(where:{$0 < 0}) {
        if dif[i-1] > 0 {
            print("NO")
            break
        }
    }
    if dif.count > 2 {
        let i = dif.endIndex-1
        if dif[i] > dif[i-1] {
            print("NO")
            break
        }
        if dif[i] == dif[i-1] {
            print("NO")
            break
        }
    }
}

print(A)
print(B)
print(dif)
/*
let input = [0,2,5,3]
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
[5, 8, 11, 14, 17, 20, 23, 26, 29, 32, 35]
[6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
런타임 오류 288 9679 9653 99


 */

```
