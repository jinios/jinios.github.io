---
layout: post
title: List와 ArrayList의 차이
tags: list ArrayList java
categories: JAVA
---



**List list = new ArrayList(); 와 ArrayList list = new ArrayList(); 의 차이?**

클래스를 생성할 때 "도형 타입"으로 생성하게 되면 도형 클래스 안에는 정사각형이 아닌 다른 직사각형, 삼각형 등 다양한 도형 형태로 인터페이스를 구현한 클래스에서 사용이 가능하다.

하지만 정사각형 클래스로 생성하게 되면 직사각형, 삼각형 등 에서는 사용할 수 없고 오직! 해당 클래스는 정사각형만 구현할 수 있다. 따라서 도형에 비유하여 풀어보면,

    List list = new ArrayList();
    // 도형 list = new 정사각형();

    ArrayList list = new ArrayList();
    // 정사각형 list = new 정사각형();

(List는 interface이다. interface는 공통되는 메소드를 추출해 놓은 클래스로 생각하면 됨..나중에 인터페이스에 대한 공부를 더 하고 추가할예정!)

출처: http://galgum.tistory.com/18
