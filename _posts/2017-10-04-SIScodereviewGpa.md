---
layout: post
title: SIS실습 코드리뷰_GPA계산 부분_인터페이스사용
tags: JAVA study 인터페이스 코드리뷰
categories: JAVA
---

공부하면서 헷갈렸던 부분들 하나하나 다 주석을 달아놓아서 엄청 복잡하고 지저분해보이는데 이렇게 뜯어보면서 코멘드를 달았던 것이 도움이 됐다. 혹시라도 또 생각이 안나면 찾아보려고 포스팅으로 저장한다!

##### 부족했던 부분
- 생성자 초기화 (전공/비전공학생인 상태에 따라서 다른 성적계산로직을 사용해야했을때)
- 변수를 바로 쓰지 않고 새로 선언 후 어딘가에 담은 후 쓰려는 버릇(학점을 int로 변환하려고했을때)
- 인터페이스를 구현한 객체 선언하기 
  - example : List< Grade> gradeList = new ArrayList<Grade>();



### Student.java 코드 리뷰

~~~ java
package sis;

import java.util.ArrayList;
import java.util.List;

import sis.Student.Grade;


 public class Student {
	 public enum Grade{
		 A, B, C, D, F;
	 } //상수의 이름을 저장 (static final string A = "A")

	 final static String IN_STATE = "경기도";

	String name;
	int credits;
	String inState;
	List<Grade> gradeList = new ArrayList<Grade>();
	private GradingStrategy gradingStrategy = new NonHonorGradingStrategy();
  //Student객체가 만들어지면 기본적으로 비전공자일때의 성적 계산로직을 사용하도록 초기화시킨다. (setGradingStrategy()메소드 참고)

	public Student(String name) {
		this.name = name;
		credits = 0;
		inState = "default";
	}

	public String getName() {
		return name;
	}

	public void setGradingStrategy(GradingStrategy gradingStrategy){
		this.gradingStrategy = gradingStrategy;
	}

	public void addCredits(int credit) {
		credits = credits + credit;
	}

	public int getCredits() {
		return credits;
	}

	public boolean isFullTime() {
		if (credits >= 12) {
			return true;
		} else {
			return false;
		}
	}

	public void setState(String state) {
		inState = state;
	}

	public boolean isInState() {
		if (inState.equals(IN_STATE)) {
			return true;
		} else {
			return false;
		}
	}

 /*
  * List를 직접 노출하지 않고 List를 구현한 객체 ArrayList를
  * 반환하는 메소드
  */
	public List<Grade> getGrades(){
		return gradeList;
	}


  /*
   * gradeList에 Grade를 add하는 메소드.
   * enum클래스타입의 (Grade) grade를 어딘가로부터 인자로 받고,
   * 리스트에 넣어주는 역할.
   */
	public void addGrade(Grade grade) {
		gradeList.add(grade);
	}

	/*
	 * 현재 학생이 받은 학점으로 평균을 계산하는 메소드
	 *
	 * 1. 학생이 받은 학점들을 더한다.
	 * 	1-1 학생이 받은 학점은 Enum클래스이므로 double형으로 변환해야한다. - 인터페이스에서 구현하고 있음 (GradingStrategy의 gradeFor()메소드)
	 * 2.더한 학점을 나눈다.
	 * 3. 평균을 반환한다.
	 */
	public double getGpa() {
		double sum = 0.0;
		double avg = 0.0;
		for (Grade grade : gradeList) {
			sum += gradingStrategy.gradeFor(grade);
		}
		avg = sum / gradeList.size();
		return avg;
	}

/*
 * grade를 double형으로 변환하는 과정에서,
 * 꺼낸 값을 다시 double타입 변수로 저장하려고 했다.
 * 바로 sum에 더하면서 변수 sum값을 바꿔주는 것으로 해결했다.
 *
 */

}
~~~


#### 인터페이스 구현 코드 리뷰

~~~ java
package sis;


//GradingStrategy.java
public interface GradingStrategy {
	int gradeFor(Student.Grade grade);
}
//Student클래스에 구현된 enum Grade 클래스에 있는 상수값을 인자로 받아서 처리

~~~

~~~ java
//HonorGradingStrategy.java
import sis.Student.Grade;

public class HonorGradingStrategy implements GradingStrategy{

	@Override
	public int gradeFor(Grade grade) {
		if (grade == Grade.A) {
			return 5;
		}
		if (grade == Grade.B) {
			return 4;
		}
		if (grade == Grade.C) {
			return 3;
		}
		if (grade == Grade.D) {
			return 2;
		}
		if (grade == Grade.F) {
			return 0;
		}
		return 0;
	}
	// 각 grade값을 int로 반환하지만 getGpa()메소드에서 double로 형변환됨

~~~
~~~java
//NonHonorGradingStrategy.java
import sis.Student.Grade;

public class NonHonorGradingStrategy implements GradingStrategy {

	@Override
	public int gradeFor(Grade grade) {
		if (grade == Grade.A) {
			return 4;
		}
		if (grade == Grade.B) {
			return 3;
		}
		if (grade == Grade.C) {
			return 2;
		}
		if (grade == Grade.D) {
			return 1;
		}
		if (grade == Grade.F) {
			return 0;
		}
		return 0;
	}
}
~~~
