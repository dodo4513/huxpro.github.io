---
layout:     post
title:      "java Reflection 예제"
subtitle:   "Java Reflection과 간단한 예제"
date:       2017-03-05
author:     "Xavy"
catalog:    true
tags:
    - Java
---

### 시작하기 전에

###### Reflection이란?

> Reflection is a feature in the Java programming language. It allows an executing Java program to examine or "introspect" upon itself, and manipulate internal properties of the program. For example, it's possible for a Java class to obtain the names of all its members and display them.
>
> Reflection은 Java의 특징이다. 실행중인 Java 프로그램이 자신을 관찰하거나, 프로그램의 내부 속성들을 조작할 수 있게 허용한다. 예를 들자면, Java 클래스의 모든 멤버를 가져와 이름을 보여줄 수 있다.

조금 오래된 글이긴 하지만, Oracle에서 위와 같은 java reflection에 대한 설명을 찾았다. - [Using Java Reflection](http://www.oracle.com/technetwork/articles/java/javareflection-1536171.html)

Java는 Class 클래스를 통해 런타임 타입정보를 제공한다. 객체에서 Class 클래스를 얻기 위해서는 getClass() 메소드를 호출하면 된다.
Class 클래스를 이용하여 런타임에 객체의 타입을 알 수 있으며, 객체 필드의 값을 조회하거나 변경할 수도 있다. 이와 같이
**런타임에 타입정보를 다루는 것을 Reflection** 이라 하며 java.lang.reflect 패키지에는 다양한 리플랙션과 관련된 API를 제공한다.

### Reflection 예제

###### Main.java

```java
package example;

public class Main {
	public static void main(String[] args) throws ClassNotFoundException {

		TargetClass targetClass = new TargetClass();
		Reflection.printPackageClassType(targetClass);
		Reflection.printFields(targetClass);
		Reflection.printMethods(targetClass);
	}
}
```

###### TargetClass.java

```java
class TargetClass {

	public String memverVariable1;

	public Integer memverVariable2;

	public TargetClass() {
	}

	public TargetClass(String field) {
		this.memverVariable1 = field;
	}

	public void method1() {
		System.out.println("call method A");
	}

	public void method2(String str) {
		System.out.println("call method B : " + str);
	}
}
```

###### Reflection.java

```java
package example;

import java.lang.reflect.Field;
import java.lang.reflect.Method;

class Reflection {

	//해당 클래스의 패키지이름과 클래스 이름을 출력한다.
	static void printPackageClassType(Object object) {

		System.out.println("printPackageClassType()");
		System.out.println("package.className : " + object.getClass().getName() + "\n");
	}

	//public 접근제한자를 가진 모든 멤버변수를 출력한다.
	static void printFields(Object object) {

		System.out.println("printFields()");

		Field[] fieldList = object.getClass().getFields();
		for (Field field : fieldList) {
			System.out.println("type : " + field.getType() + " / name : " + field.getName());
		}
		System.out.println();
	}

	//public 접근제한자를 가진 모든 메서드를 출력한다.
	static void printMethods(Object object) {

		System.out.println("printMethods()");

		Method[] methodList = object.getClass().getMethods();
		for (Method method : methodList) {
			System.out.println("method name : " + method.getName());
		}
	}
}
```

###### 실행 결과

```text
printPackageClassType()
package.className : example.TargetClass

printFields()
type : class java.lang.String / name : memverVariable1
type : class java.lang.Integer / name : memverVariable2

printMethods()
method name : method1
method name : method2
method name : wait
method name : wait
method name : wait
method name : equals
method name : toString
method name : hashCode
method name : getClass
method name : notify
method name : notifyAll
```

### 참고

[Using Java Reflection](http://www.oracle.com/technetwork/articles/java/javareflection-1536171.html)

[자바 리플렉션에 대한 오해와 진실](https://kmongcom.wordpress.com/2014/03/15/%EC%9E%90%EB%B0%94-%EB%A6%AC%ED%94%8C%EB%A0%89%EC%85%98%EC%97%90-%EB%8C%80%ED%95%9C-%EC%98%A4%ED%95%B4%EC%99%80-%EC%A7%84%EC%8B%A4/)

[Java reflection과 객체구조탐색](http://www.nextree.co.kr/p3643/)

[자바(Java) Reflect 예제](http://mysnyc.tistory.com/42)
