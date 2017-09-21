---
​---
layout: post
title: 야구게임 리팩토링 연습 & 새로운 부분 공부
tags: java refactoring 구현연습
categories: JAVA 
​---
---





#### 몰랐던 부분 / 알게 된 부분 

- 메소드 return의 쓰임 : 

  - 메소드의 리턴값은 그 메소드를 호출한 곳으로 전달됩니다. 고로 해당 리턴값을 쓸 곳에서 메소드를 호출하면 되고 / 그 메소드가 어딘가로 값을 전달해야할 의무를 가지고 있다면  **return**을 넣어주면 됩니다.
  - 메소드는 조건문이나 반복문 안에서라도 return문을 만나면 바로 종료됩니다.

- nullpointerException : 객체의 선언과 생성. 

  객체가 없는데 메소드를 사용하려고할때 해당 에러가 발생합니다.

  메소드를 사용하기 위해서는 객체를 생성해줍니다! (선언만 클래스에서 하고 메소드 안에서 new 객체(); 해서 생성해줄 수 있음)

  [이 블로그 글](https://m.blog.naver.com/PostView.nhn?blogId=geeyoming&logNo=220078572377&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)에서 설명을 참고함. 

- 생성자와 초기화  : [내 블로그](https://youth27.github.io/%EC%83%9D%EC%84%B1%EC%9E%90/%EB%B3%80%EC%88%98/%ED%95%84%EB%93%9C/%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4/%EC%B4%88%EA%B8%B0%ED%99%94/2017/09/20/Constructor/)에 정리함!

- 클래스 변수 : 인스턴스변수를 어떤 다른 메소드끼리 공유할 일이 있다면 필드값으로 저장해두고 씁니다. 

  그리고, 객체에 저장된 값을 어딘가에서 쓸 것이라면 해당 속성값을 받을 수 있는 **getter 메소드**를 항상 가지고있어야합니다! 

  ~~~java
  public class GamePlay {
  	ArrayList<Integer> answerNumbers;

  	// 최초 실행 시 Gameplay객체가 answerNumbers를 갖고있도록 생성자 설계
  	public GamePlay() {
  		GetAnswer getAnswer = new GetAnswer();
  		this.answerNumbers = getAnswer.getAnswer();
  	}
  ~~~

  ​

   


#### 야구게임 리팩토링 todo

[(야구게임 연습 repo바로가기)](https://github.com/youth27/java-whitelevel-review/tree/master/src/baseballPractice)

- 야구게임 : 3자리 수를 입력받아서 정답을 맞춘다
- 규칙

1. 같은 자리수와 숫자가 맞으면 스트라이크

2.  자리수는 다르지만 숫자가 같으면 볼

3.  일치하는게 없을 경우는 아웃

4.  3 스트라이크면 게임 종료

   ​

- 1회 게임을 하는데에 필요한 것

1. 정답이 있엉해요 - 최초 한번만 실행 게임이 실행됐을때 한번만
2.  3자리수를 사용자가 입력한다.
   1. 예외처리 (같은 수 입력, 0 입력 등)
3. 사용자가 입력한 값이 맞는지 비교한다.
   1.  3스트라이크가 아니면 2번으로
   2. 3 스트라이크면 게임 종료