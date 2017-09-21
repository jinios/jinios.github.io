---
layout: post

title: 생성자와 초기화, this, 필드와 변수

tags: java 생성자 변수 필드 인스턴스 초기화

categories: JAVA
---





### 필드와 변수

클래스의 구성 = 필드 + 메소드 + 생성자

객체의 구성 = 필드 + 메소드

객체에는 생성자가 없다. 



#### 필드와 변수의 선언위치

변수와 필드는 다르다!

변수(지역변수) / 필드(멤버변수, 전역변수)

전역변수 : 클래스 내의 멤버변수로, heap영역 메모리에 저장됨. 자동으로 초기화됨. (int의 경우 0, boolean은 false, String은 null)

지역변수 : 메소드나 생성자 내의 변수, stack에 저장됨. stack에 할당된 변수는 자동으로 초기화되지않음.

**필드는 클래스 내부 중괄호 { } 안**에서 어디든지 선언이 가능하지만 **메소드와 생성자의 요소 밖**에만 생성이 돼야함.

**변수는 메소드와 생성자 중괄호 { } 안**에 선언이 돼야함.



![스크린샷 2017-09-20 오후 5.47.25](/Users/jeonmijin/Desktop/스크린샷 2017-09-20 오후 5.47.25.png)



#### 생성자와 초기화

클래스의 3대 구성요소 : 필드, 메소드, 생성자

생성자는 객체를 생성하고 초기화하는 역할을 담당한다. 모든 클래스에는 생성자가 반드시 한 개 이상 존재하며, 따로 선언을 하지 않더라도 보이지 않는 디폴트 생성자가 자동 추가되어 기본적으로는 선언을 대신해준다. (but 클래스 내부에 선언한 생성자가 하나라도 있으면 디폴트 생성자를 생성되지 않는다.)

~~~
Card c = new Card();
1. new에 의해서 메모리 heap영역에 Card클래스의 인스턴스생성
2. 생성자 Card()가 호출되고 수행(생성)된다. 
3. 생성된 Card인스턴스의 주소가 참소변수 c에 저장된다.
(여기서 Card()부분은 컴파일러가 만든 기본생성자를 호출하는 부분임)
~~~







#### 생성자의 조건

- 클래스명과 동일, 리턴타입 없음
- 매개변수가 다른 생성자를 여러개 만드는 **오버로딩**이 가능함
- new 연산자를 이용해서 선언
- 주로 필드(멤버변수)의 초기화를 위해 사용한다.
- this와 super로 생성자를 호출할 수 있다.

#### 필드 초기화

동일한 이름을 가지는 클래스안의 여러 객체들은 처음에 모두 똑같은 데이터를 가지고 있음. 그런데 **그 객체마다 값을 변경해주기위해 초기화를 한다.**

~~~java
public class Student{
  String name;
  
  public Student(String studentname){
    String name = studentname;
  }
  
  public static void main(String[] args){
    Student s1 = new Student("쿠키");
    Student s2 = new Student("사과");
    
    System.out.println(s1.name);
    System.out.println(s2.name);
  }
}
~~~



결과화면 :

```
쿠키
사과
```



#### 매개변수가 있는 생성자 예제

- 인스턴스 변수를 따로 초기화해줄필요없이, 인스턴스를 생성하는 동시에 원하는 값으로 초기화 할 수 있도록 만든다.
- 이렇게 하면 객체를 사용하기 위해서 필수적인 값을 매개변수에 넣어야만 객체가 생성될 수 있도록 강제함으로써, 더 안정적인 코딩이 가능하다. 

~~~java

public class CarTest {
	String color;
	String gear;
	int door;

	// 기본생성자
	public CarTest() {
	}

	// 매개변수가 있는 생성자
	public CarTest(String c, String g, int d) {
		this.color = c;
		this.gear = g;
		this.door = d;
	}

	public static void main(String[] args) {
		CarTest c2 = new CarTest();
		c2.color = "black";
		c2.gear = "manual";
		c2.door = 4;

		System.out.println(c2.color);
		System.out.println(c2.gear);
		System.out.println(c2.door);

		CarTest c1 = new CarTest("white", "auto", 5);
		System.out.println(c1.color);
		System.out.println(c1.gear);
		System.out.println(c1.door);

	}

}

~~~



#### 생성자 호출 this()

- this - 인스턴스 자신을 가리키는 참조변수 
- this(), this(매개변수) -  생성자, 같은 클래스 내의 다른 생성자를 호출할때 사용한다.

~~~java

public class CarTest2 {
	String color;
	String gear;
	int door;
	
	/*
	 * this를 이용한 생성자 호출은 맨 첫째 줄에서만 가능하다. 
	 * 생성자 내에서 초기화 작업도중에 다른 생성자를 호출할 수 없기때문이다.
	 */
	public CarTest2(){
		this("white", "auto", 2);
	}

	public CarTest2(String color){
		this(color,"manual",4);
	}
  
	/*
	 * this.color부분은 인스턴스변수, color부분은 파라미터로 받은
	 * 지역변수 할당   
	 */
	public CarTest2(String color, String gear, int door){
		this.color = color;
		this.gear = gear;
		this.door = door;
	}
}

~~~





