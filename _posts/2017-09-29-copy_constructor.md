---
layout: post
title: 생성자에서 다른 생성자 호출, 생성자를 이용한 인스턴스의 복사
tags: JAVA study 생성자 this
categories: JAVA
---

- 생성자 간에도 서로 호출이 가능하다.
- this, this(매개변수) : 생성자, 같은 클래스의 다른 생성자를 호출 할 때 사용한다.
- 생성자의 이름으로 클래스임름 대신 this를 사용한다.
- 한 생성자에서 다른 생성자를 호출할때는 반드시 첫 줄에서만 호출이 가능하다.

- 한 클래스의 (같은 클래스)의 참조변수를 매개변수로 선언한 생성자를 만들 수도 있다.(생성자를 이용한 인스턴스의 복사)
> 현재 사용하고 있는 인스턴스와 같은 상태를 갖는 인스턴스를 하나 더 만들고자 할때 생성자를 이용할 수 있다. 두 인스턴스가 같은 상태를 갖는다는 것은 "두 인스턴스의 모든 인스턴스변수가 동일한 값을 갖고있다는걸 뜻한다."
~~~java
Car(Car c){
color = c. color;
door = c.door;
}
~~~
위의 코드는 Car 클래스의 참조변수c를 매개변수로 선언한 생성자이고, 참조변수 c가 가리키는 Car인스턴스의 인스턴스 변수인 color, door의 값을 인스턴스 자신으로 복사한 것이다.

#### <예제>
~~~java
package studyTest;

public class ConstructorTest {
	String color;
	int door;

//this(매개변수)를 통해서 같은 클래스 내에 있는 생성자 호출 : c1
  public ConstructorTest() {
		this("red", 4);
	}

	//c1을 인자로 받는 생성자 : c2
	public ConstructorTest(ConstructorTest c1) {
		color = c1.color;
		door = c1.door;
	}

  //String color와 int door를 매개변수로 가지는 생성자 : c
	public ConstructorTest(String color, int door) {
		this.color = color;
		this.door = door;
	}

	public static void main(String[] args) {
		ConstructorTest c = new ConstructorTest("white", 2);
		ConstructorTest c1 = new ConstructorTest();
		ConstructorTest c2 = new ConstructorTest(c1);

		System.out.println("c의 컬러 : " + c.color);
		System.out.println("c의 문 : " + c.door);
		System.out.println("=====================");
		System.out.println("c1의 컬러 : " + c1.color);
		System.out.println("c1의 문 : " + c1.door);
		System.out.println("=====================");
		System.out.println("c2의 컬러 : " + c2.color);
		System.out.println("c2의 문 : " + c2.door);

		c1.door = 100;

		System.out.println("=========c1변경후==========");
		System.out.println("c1의 컬러 : " + c1.color);
		System.out.println("c1의 문 : " + c1.door);
		System.out.println("=====================");
		System.out.println("c2의 컬러 : " + c2.color);
		System.out.println("c2의 문 : " + c2.door);

	}

}
~~~
#### <결과>
~~~
c의 컬러 : white
c의 문 : 2
=====================
c1의 컬러 : red
c1의 문 : 4
=====================
c2의 컬러 : red
c2의 문 : 4
<<<<<<<<<< c1변경후 >>>>>>>>>>>
c1의 컬러 : red
c1의 문 : 100
=====================
c2의 컬러 : red
c2의 문 : 4
~~~

인스턴스 c2은 c1을 복사하여 생성된 것이므로 서로 같은 상태를 갖지만, **독립적인 메모리 공간에 존재하는 별도의 인스턴스이기때문에** c1값이 변경되어도 c2는 영향을 받지 않는다.  
