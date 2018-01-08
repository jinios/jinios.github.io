디버그모드에서 `po 변수명`하면 해당 변수에 해당되는 값을 디버그 창에 찍어볼 수 있다!

## TDD강의
타겟 - + unitTest bundle추가

테스트코드를 먼저 작성!
행동패턴 세 가지를 계속반복!
- Red
- Green
- Refactor

객체를 만든 것을 확인 - 꼭 있어야 한다! XCTAssertNotNil()
init안에서 init함수를 부를때
```swift
init() {
  self.init(player:player)
}
```
지정생성자
빈값 리턴할때 `[[Int]]()` > `.init`을 바로 쓸 수 있다!
failable initializer

제너릭
struct Stack 되어있던걸 다 struct Stack<T>로 바꾸고 제너릭타입으로 변경하면 타입을 외부에서 지정: `var stack = Stack<String>` 하고 채택한 타입을 받아서 동작할 수 있게 변경!
`struct Stack<T: CustomStringConvertible>` 이런식으로 타입을 제한할 수도 있다! (T자리에 올 수 있는 타입은 오직 CustomStringConvertible프로토콜을 준수하는 타입만 올 수 있다는 제한)

tip: 리팩터메뉴에서 rename하면 호출하는 곳에서까지 이름들 다 바꿔줌 (ctrl cmd e는 edit in scope라서 해당 file안에서만 바뀜)

추상화 & 다형성 할때
로직이 똑같고 타입이 여러개면 제네릭이 좋음!
구현제가 여러갠데 메소드 형태가 똑같고 구현이 다를때, 구현은 다르지만 하나의 코드로 (메소드 이름이 같으니까) 호출할 수 있을때 - 프로토콜이 좋음!
상속 - (프로퍼티중심)프로퍼티가 똑같을때 이 속성을 공통속성으로 만들어서 부모의 속성으로 넣고 상속을 받으면 서브클래스에 그 프로퍼티가 없어도 부모가 가진걸로 쓸 수 있을때 / 프로퍼티가 중심이지만 메소드가 같으면 메소드도 공통적으로 쓸 수 있음
