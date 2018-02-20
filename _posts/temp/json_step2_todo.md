
어떤 형태가 입력되든 분석대상은 분리해서 parsingTarget타입으로 선언한다.
객체가 몇 개 들어있는가는 parsingTarget의 수로 분간 할 수 있으면...좋은데
ㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠ



- dictionary를 만들어서 value만 배열로 담아서 리턴
- [ ]만 있을때 분석해야할 값을 value만 담앗던것처럼
  - parsingTarget 객체로 감싸기
- 분석대상만 남은 parsingTarget을 objectCounter에 넘기기




### 문자열 자르기
- 첫번째 괄호가 무엇인지 판단
  - [ 일때
    - 내부에 {}가 있음
    - 내부에 {}가 없음 - values만 있으니 typeIdentifier타입 체크
  - { 일때 - key:value 쌍만 있으니 typeIdentifier타입 체크

- JSONData로 통용되는 enum이 있어야 함
- JSONData안에는 string/Int/Bool/{}dictionary가 있음
- JSONData 수 만큼 카운팅 가능해야함
- JSONData내부의 수 만큼도 카운팅 가능해야함
https://stackoverflow.com/questions/27548239/swift-different-objects-in-one-array

{"name" : "KIM JUNG", "alias" : "JK", "level" : 5, "married" : true}
[{"name" : "KIM JUNG", "alias" : "JK", "level" : 5, "married" : true}, "KIM JUNG", 5, "married", true]
[true,"hong"]
