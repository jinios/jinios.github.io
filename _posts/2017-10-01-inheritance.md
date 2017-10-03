---
layout: post
title: 170828_코드스쿼드_상속, 추상화, super, 오버라이딩
tags: JAVA 상속 abstract super
categories: Codesquad
---



### 상속

#### 프로그래밍에서 중복이 생겼을때 제거하는방법

- 메소드
- 클래스 간의 중복제거 
  - 상속
  - 조합 - 새로운 클래스만들어서 불러내기


#### 부모클래스 / 자식클래스

자식은 부모가 가지는 메서드와 필드를 그대로 상속 - 메서드 호출로 그대로 가져다 쓸수있다.

**생성자와 초기화 블럭은 상속되지 않는다.** 멤버만 상속된다. 

- 부모클래스의 디폴트생성자는 무조건 호출됨
  - '부모클래스가 생성이 되어야 자식클래스가 존재할 수 있다'고 생각하면 편함
  - 부모클래스의 디폴트생성자는 만들어졌다가 상속됨과 동시에 사라진다고 생각하면 됨
- 자손 클래스의 멤버 개수는 조상 클래스보다 항상 같거나 많다. (자손클래스의 사이즈 > 부모 클래스의 사이즈)
- 자손 클래스에 무언가 추가되더라도 부모클래스에 영향을 미치지는 않는다.
- **접근제어자 protected**: 다른패키지이더라도 상속받은 클래스는 접근가능



#### 추상화 abstract

(부모는 자식클래스의 기능은 가져다 쓸 수 없지만, 강제화를 통해서 할 수 있다. - abstract를 통해서 할 수 있다.)

구현되지 않은 메서드를 가져다 쓸 수 있음 / abstract로 선언한 메소드를 자식 클래스에서 반드시 구현해야 한다.

~~~java
//부모클래스
package study.extend;

	public abstract class CaffeineBeverage {
		protected void prepareRecipe() {
	        boilWater();
	        brew();
	        pourInCup();
	        addCondiments();
	    }
		
		abstract void brew();
		abstract void addCondiments();
     //CaffeinBeverage클래스에는 없고 자식클래스에 구현되어있는 메소드	
		protected void boilWater() {
	        System.out.println("물을 끓인다.");
	    }

	    protected void pourInCup() {
	        System.out.println("컵에 붓는다.");
	    }
	}
~~~



~~~java
//자식클래스
package study.extend;

public class Coffee extends CaffeineBeverage {
    
    public void brew() {
        System.out.println("필터를 활용해 커피를 내린다.");
    }

    public void addCondiments() {
        System.out.println("설탕과 우유를 추가한다.");
    }
}
~~~



추상화된 부모 클래스에 인터페이스 implements했을때, 인터페이스를 위해 구현해놓은 메소드가 없으면 

: 자식 클래스에 컴파일 에러가 난다!

: 부모 클래스는 이미 추상화되었기때문에 구현하지 않은 메소드에 대해서 pass가 가능하다. 

:만약 부모 클래스에 구현을 해놓았으면 자식 클래스에서는 컴파일에러가 안남.





#### 업캐스팅

자식(클래스)을 부모로 바꿈

대신에, 인스턴스를 만들어서 실행하는 경우에는 업캐스팅을했을땐 부모가 가지고있는 메소드만 호출할수있음



#### 다운캐스팅

부모(클래스)를 자식으로 바꿈



####오버라이딩

자식클래스가 부모클래스에 있는 메소드를 재정의하면 (기능을 바꾼다거나) 부모에 있는 메서드가 무시되고 자식에 있는 메서드가 실행된다. 이런 점은 자식클래스에서 재정의된 메소드에 @Override어노테이션으로 표시를 해줘서 명시한다.  (cf. implements - 그대로 구현)

~~~java
public class Coffee extends CaffeineBeverage {
//자식클래스가 boilWater메소드를 오버라이드 한 상황
  
	@Override
	public void boilWater() {
        System.out.println("찬물을 끓인다.");
    }
	
    public void brew() {
        System.out.println("필터를 활용해 커피를 내린다.");
    }

    public void addCondiments() {
        System.out.println("설탕과 우유를 추가한다.");
    }
}
~~~

~~~java
package study.extend;

	public abstract class CaffeineBeverage{
		protected void prepareRecipe() {
	        boilWater();
	        brew();
	        pourInCup();
	        addCondiments();
	    }
		
		abstract void brew();
		abstract void addCondiments();
		
		protected void boilWater() {
	        System.out.println("물을 끓인다.");
	    }
	        protected void pourInCup() {
	        System.out.println("컵에 붓는다.");
	    }
	}
~~~

결과는  boilWater메소드를 실행했을때 자식클래스에서 재정립한 "찬물을 끓인다."가 표시된다. 

#### 오버라이딩의 조건

오버라이딩은 메서드의 내용만을 새로 작성하는 것이므로 메서드의 선언부는 부모클래스와 완전히 일치해야한다. 

- 이름 /  매개변수 / 리턴타입이 모두 동일해야한다.
- 접근 제어자를 부모클래스의 메서드보다 좁은 범위로 변경 할 수 없다.
- 예외는 부모클래서의 메서드보다 많이 선언할 수 없다.
- 인스턴스메서드를 static 또는 그 반대로 변경할 수 없다.



#### Super

- 자식 클래스에서 부모 클래스로부터 상속받은 멤버를 참조하기 위해서 씀 
- 상속받은 멤버와 자신의 클래스에 정의된 멤버의 이름이 같을때 super 키워드로 구별한다. (중복 정의되어 구별할때 super를 사용하는 것이 좋다.)
- **static 메서드는 인스턴스와 관련이 없다. this와 마찬가지로 super역시 static메서드에서는 사용할 수 없고 인스턴스메서드에서만 사용가능하다.**(static 메서드는 클래스를 통해서 바로 접근이 가능하니까 this와 super를 쓸일없음ㅎㅎ)

자식클래스에서 부모클래스에 있는 생성자/필드/메서드에 접근하기 위해서 씀

부모에 생성자가 있을때는 자식클래스에게 생성자를 인자로 전달해주지않으면 컴파일에러가 남

부모 인스턴스는 만들어지지 않음

부모클래스에 기본생성자가 만들어지지않고도 사용할 수 있는 방법

부모클래스에 생성자가 있는 인스턴스가 있을때

부모클래스에도 생성자인자가 있고 자식클래스에도 생성자가 있어야할때

super(a,b) - 부모 클래스의 생성자를 의미함 = 자식 클래스에서 a,b를 가지는 자기 자신의 초기화가 필요할때 

하위클래스가 인스턴스화 된다는 것은 상위 클래스가 인스턴스 화 되었다는 뜻

= 하위 클래스가 초기화 되려면 상위 클래스도 초기화 되어야 한다. 

~~~java
package study.extend;


public class Parent {
	int x = 10;
}
public class Child extends Parent {
	int x = 20;
	
	void method() {
		System.out.println("x="+ x); //20
		System.out.println("this.x="+this.x); //20
		System.out.println("super.x="+super.x); //10
	}

}
~~~

~~~java
package study.extend;

	public abstract class CaffeineBeverage{
		String beverage = "super.beverae 카페인음료";
		public CaffeineBeverage() {
			System.out.println("카페인 기본생성자 ");
		}
		
		public CaffeineBeverage(String string) {
			System.out.println(string);
				}

		protected void prepareRecipe() {
			boilWater();
	        brew();
	        pourInCup();
	        addCondiments();
	    }
		
		abstract void brew();
		abstract void addCondiments();
		
		protected void boilWater() {
	        System.out.println("물을 끓인다.");
	    }

	    protected void pourInCup() {
	        System.out.println("컵에 붓는다.");
	    }
	}

--------------------------------------------------------

public class Coffee extends CaffeineBeverage {
	String beverage="커피음료";
	public Coffee() {
		super("슈퍼생성자 호출!!");//부모클래스 생성자 호출하면서 "슈퍼생성자호출"문장 전달 
		System.out.println("커피음료 생성자 ");
	}
	
	@Override
	public void boilWater() {
		super.boilWater(); //"물을 끓인다 " 부모클래스에 있는 boilWater()메소드 
        System.out.println("찬물을 끓인다.");
    }
	
    public void brew() {
        System.out.println("필터를 활용해 커피를 내린다.");
    }

    public void addCondiments() {
        System.out.println(super.beverage + "를 추가한다.");
    }
}

~~~

<결과>

슈퍼생성자 호출!!
커피음료 생성자 
물을 끓인다.
찬물을 끓인다.
필터를 활용해 커피를 내린다.
컵에 붓는다.
super.beverae 카페인음료를 추가한다.





#### Object

자바의 모든 클래스는 기본으로 Object를 상속받는다.

toString(), equals(), toString()과 같이 메서드를 따로 정의하지 않아도 사용할 수 있었던 이유는 자바의 클래스가 Object를 상속받았기 때문 (Object에는 모든 인스턴스가 기본적으로 가져야할 메서드11가지가 구현되어있음)





