---
layout: post
title: Java- String, StringBuilder, for-each문
tags: JAVA StringBuilder String
categories: JAVA

---


### String

- 스트링은 불변함 (immutable), 때문에 String과 String을 더하면 새로운 String 객체를 생성한다.

> "쿠키"+"안녕!"

>A instance + B instance 했을때 A+B가 되는게 아니라 두개가 더해진 C가 만들어진다. (메모리를 낭비하게 되는 단점) 이 단점을 보완하기 위해 StringBuilder 클래스를 사용한다.

- 스트링빌더 StringBuilder
  - 스트링빌더는 기존 데이터에 새로운 데이터를 더하는 방식을 취함 (A+B형태)
  - 짧은 문자열을 더할때는 메모리 문제가 치명적이지 않겠지만, 긴 문자열을 더하는 상황에는 StringBuilder를 쓰는게 더 효율적

    @Test
    ~~~
        public void appendCheck () {
            assertEquals("코드스쿼드 화이트 레벨 수강생은? 18 명이다.", createMessage(18));
        }
    //appendCheck메소드가 하는 일 : assertEquals - creatMessage에 인자 int 18을 넣은 결과가 "코드스쿼드 화이트 레벨 수강생은? 18 명이다."인지 확인하는 일

    private String createMessage(int numberOfClass) {
            StringBuilder sb = new StringBuilder(); //스트링빌더 클래스 객체를 만든다
            sb.append("코드스쿼드 화이트 레벨 수강생은? "); //어펜드메소드에 문자열 입력(인자전달) 객체.메소드(파라미터);
            sb.append(numberOfClass); //파라미터가 정수형이어도 문자로 바꿔서 저장
            sb.append(" 명이다.");
            return sb.toString(); //스트링빌더에(sb.append(문자열);) 안에 입력된 문자열들을 하나의 문자열로(string) 출력
~~~
### for - each 문
~~~
    ArrayList<String> list = new ArrayList<>;
    for (String member : list){
      systout("member");
    }

    //결과는 member1,2,3...으로 출력!
~~~

### 참조변수의 선언

- 클래스 변수명; / Student a;  : student클래스를 타입으로 가지는 참조변수 a

~~~
    for (Student student : students) {
    			sb.append(student.getName()); //1번
      			sb.append(student.name); //2번
     			sb.append(NEWLINE);
    		}
    //1번이나 2번을 썼을때 나타나는 결과값은 같지만 1번이 더 권장되는 방법이다. (학생의 이름이 결과값)
    ~~~
