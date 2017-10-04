---
layout: post
title: SIS 실습 해설 주석 코드
tags: JAVA SIS TDD 코드리뷰
categories: JAVA
---



공부하면서 헷갈렸던 부분들 하나하나 다 주석을 달아놓아서 엄청 복잡하고 지저분해보이는데 이렇게 뜯어보면서 코멘드를 달았던 것이 도움이 됐다. 혹시라도 또 생각이 안나면 찾아보려고 포스팅으로 저장한다!



### 초기화메서드 @Before

제이유닛 테스트메소드는 순차적으로 실행되는것이 아니고 지정해서 실행하고싶은 테스트메소드 만 실행할 수 있음. 

초기화메서드가 있을땐 초기화메서드 > 실행메서드1 > 초기화메서드 > 실행메서드2 … 이런식으로 실행됨

```java
	  @Before
    public void setup() {
		  session = new CourseSession("ENGL", "101");
    }

	  @Test
    public void create() throws Exception {
        assertEquals("ENGL", session.getDepartment());
        assertEquals("101", session.getNumber());
        assertEquals(0, session.getNumberOfStudents());
    }
    
    @Test
    public void enroll() throws Exception {
        Student firstStudent = new Student("쿠키");
        session.enroll(firstStudent);
        assertEquals(1, session.getNumberOfStudents());
        ArrayList<Student> allStuents = session.getAllStudents();
        assertEquals(1, allStuents.size());
        assertEquals(firstStudent, allStuents.get(0));

        Student secondStudent = new Student("애플");
        session.enroll(secondStudent);
        assertEquals(2, session.getNumberOfStudents());
        assertEquals(2, allStuents.size());
        assertEquals(firstStudent, allStuents.get(0));
        assertEquals(secondStudent, allStuents.get(1));
    }
```



#### Student.java

```java
package sis;
public class Student {
	String name;
	Student(String name) { //생성자(메소드) - 클래스와 똑같은이름 
		this.name = name; // name에 name으로 받은 인자를 전달 (값 할당)
	}					
  
  Student student = new Student(String name); //왜 이렇게하면안될까? >> 인스턴스의 생성자와 인스턴스 선언하는 것이 헷갈려서 메모했던 것 같다. 위의 메소드는 생성자 메소드, 이 문장은 인스턴스를 선언하는 코드! (20170927 추가)
  
  //여기서 this는 Student에 선언된 인스턴스변수 String name을 가르킨다. (이름이 똑같기때문에 Student생성자에서 새롭게 선언된 name이 아니라 인스턴스 변수 그대로를 받는 것이라고 지정해줌)
  
	String getName() {
		return name;
	}
}//이 클래스는 student 객체를(이름을 할당받음으로써)만들고, getName()메소드로 이름을 리턴하는 역할을 한다.
```



#### CourseSession.java

이 CourseSession클래스는 같은 패키지에 있는 Student를 통해서 객체를 생성하고 수강신청기능을 담당한다.

```java
package sis;

import java.util.ArrayList;
import java.util.Calendar;
import java.util.Date;

public class CourseSession {
	static final String NEWLINE = System.getProperty("line.separator");
	static final String ROSTER_REPORT_HEADER = "학생 목록" + NEWLINE + "-----" + NEWLINE;
	static final String ROSTER_REPORT_FOOTER = NEWLINE + "학생 수 : ";
	
	String department;
	String number;
	Date startDate;
	ArrayList<Student> students = new ArrayList<>();
  //클래스변수, 클래스 내의 모든 변수에서 사용 가능하다.
  //같은 패키지, 다른 클래스에서 사용할 수 있는 변수 (default접근제어자)

	public CourseSession(String department, String number, Date startDate) {
		this.department = department;
		this.number = number;
		this.startDate = startDate;
	}//CourseSession생성자는 클래스에 이미 선언된 인스턴스 변수 그대로를 받는다

	String getDepartment() {
		return department;
	}

	String getNumber() {
		return number;
	}

	int getNumberOfStudents() {
		return students.size();
	}

	public void enroll(Student student) {
		students.add(student); 
	}
	//위의 세 메소드들과 다르게 인자를 받는 이유는 enroll메소드의 경우, student 객체가 필요하기때문에!
  
	public void drop(Student student) {
		students.remove(student);
	}

	public Student findStudent(int index) {
		return students.get(index);
	}

	public Date getStartDate() {
		return startDate;
	}

	public Date getEndDate() {
		Calendar date = Calendar.getInstance(); //Calendar클래스를 쓰기위해 import
		date.setTime(startDate);
		// date.set(2017, Calendar.JULY, 31); //테스트 코드에서 Before에 추가해서 초기값으로 고정함
		date.add(Calendar.DAY_OF_YEAR, 7 * 6 - 3); // 6주 후 & -3일
		return date.getTime(); //Calendar클래스의 getTime메소드
	}

	public String getRosterReport() {
		StringBuilder sb = new StringBuilder(ROSTER_REPORT_HEADER);
      //스트링 빌더 사용을 위해 객체를 지정할때 인자에 넣어준 값이 항상 스트링빌더를 사용하는 처음에 출력됨. (헤더 부분 내용을 넣으면 됨)
		
		for (Student student : students) {
			sb.append(student.getName());
			sb.append(NEWLINE);
		}
		sb.append(NEWLINE);
		sb.append("학생 수 : ");
		sb.append(students.size());
		
		return sb.toString();
	}

}
```

