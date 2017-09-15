---
layout: post
title: 170802_코드스쿼드 화이트 수업정리
tags: codesquad study java
categories: Codesquad
---

#### Arraylist 선언할때
~~~
ArrayList<클래스타입>리스트명=new ArrayList<>();
//<>부분에 int말고 Integer와 같은 클래스 타입으로선언하기
~~~

#### 찾아볼 용어
- Primitive type : 자바의 데이터 타입 중 기본형 - 메모리 상에 할당되는 각각의 공간에 실제 값이 들어감. (cf. reference type은 다른 것을 참조하기 위한 주소값이 들어감)

- 제너릭스(Generics): ArrayList(혹은 컬렉션 클래스)가 다룰 객체를 <>안에 미리 명시해줌으로써 형변환을 하지 않고 사용할 수 있게 함. ArrlayList가 사용할 객체의 타입이란 이야기. [제네릭스에 대한 설명은 이 블로그를 참고했다.](http://arabiannight.tistory.com/entry/%EC%9E%90%EB%B0%94Java-ArrayListT-%EC%A0%9C%EB%84%A4%EB%A6%AD%EC%8A%A4Generics%EB%9E%80)

- 자바컨벤션 : 자바 언어의 (코딩)작성규칙
- jsp :
- whitelabel error : 수업 복습시간에 회원가입 페이지 서버배포를 연습하다가 난 에러인데, 화이트라벨이 어떤 뜻이 있는게 아니라 스프링부트는 에러가 나면 기본적으로 whitelabel error page로 이동한다고 한다.


#### html에서 bootstrap css가져올때

- ../ 상대경로
ex) 현재경로의 부모경로로 가서 css를 찾아서 bootstrap을 찾아간다

- / 절대경로 > static에서 시작!
ex) "static/css/bootstrap.min.css"이라는 경로로 됨

(static 폴더에서 index.html을 읽어옴 / 정적인 페이지)



#### 야구게임 웹 구현 솔루션
- 컨트롤러 메소드 = 메인메소드
- 사용자의 입력을 받는 부분은 콘솔이 아닌 html을 통한 화면으로 변경
- 결과를 보여주는 화면 또한 콘솔 > 웹 화면으로 변경
메인 로직을 구현하는 부분은 똑같음
- (어렵다면 야구게임 메소드 분리 연습하기! MVC기능에 맞춰서 분리)


#### 서버에 요청을 보낼 때
- http method에 GET방식과 POST방식으로 요청을 보낼 수 있다
  - GET방식일때 : 주소에 입력값이 다 보임. 비밀번호와 같은 민감정보가 노출 될 수 있다. 아래처럼 method에서 post로 변경해줘야함

  ~~~
  <form name="question" method="post" action="/users">
  ~~~

- 앞에서 전달할때 실행했던 메소드의 매핑방식으로(@어노테이션) html에 매핑을 해야함!
@getmapping("users") or @postmapping("users")


#### 소스코드를 서버에 배포하기

- 터미널에서 aws key 있는 경로로 이동
  - (cd 디렉토리명 / ls로 확인 ... / pwd / ls -al / git pull / )

  - ssh찾기 : aws페이지 - instance / 내 서버 선택  / connect 에서 ssh 찾을 수 있음


#### 회원가입 프로그램 배포하다가 나타난 문제
로컬에서는 user/list/profile 등 html 파일 간 다 넘어가면서 서버로 올리니까 값 넘기는게 되지 않았다.
  - 속도가 엄청 느린 문제. 아예 멈춘것같아 보이기
  - 회원가입 창은 뜨는데 입력하고 제출하면 회원가입완료화면이 안뜸
whitelabel error

##### 시도했던 방법들
- kill 하고 다시 pull
- 잘못 의심되는 코드 주석처리 (baseballmain)
- 깃에 몇개 빼고 푸시해서 그런가 하고 잘못의심되는 코드 주석처리하고 전체 푸시

솔루션을 다 했는데도 안됨...
그러다 갑자기 됨
(무엇이 문제였을까 ㅠㅠㅠㅠ whitelabel error에 대해서 찾아봐야겠다.)



> 유용한 이클립스 단축키
>
> command shift t - 클래스 찾아열기 단축키
>
>command shift r - 폴더찾아열기 단축키
