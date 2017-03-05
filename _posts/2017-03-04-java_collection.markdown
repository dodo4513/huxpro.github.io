---
layout:     post
title:      "java Collection Framework - Iterable, Collection, List, Queue, Set, Map"
subtitle:   "Java Collection Framework 중 각 Interface에 대해 알아보자 - Iterable, Collection, List, Queue, Set, Map"
date:       2017-03-04
author:     "Xavy"
catalog:    true
tags:
    - Java
---

### 시작하기 전에

###### Java Collection Framework란?

Java에서 collection framework란 다수의 데이터를 쉽고 효과적으로 처리할 수 있는 표준화된 방법을 제공하는 클래스의 집합을 의미한다. 
즉, 데이터를 저장하는 자료 구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스와 인터페이스로 구현해 놓은 것이다.

###### Java Collection의 목표

Collection Framework는 최초 아래와 같은 목표를 가지고 디자인되었다고 한다.

- Framework는 고성능이어야 한다.
- 각 Collection은 유사한 방법으로 사용 가능해야 한다.
- Collection은 서로 간 상호작용이 가능해야 한다.
- Collection은 적용과 확장이 쉬워야 한다.

### Iterable, Collection, List, Queue, Set, Map 인터페이스들

<img class="shadow" alt="java collection interface" src="/img/my-post/4_java_collection/1_interface.png" >

위에서 인터페이스 간의 선은 상속을 의미한다.
각각의 인터페이스를 [Java docs](https://docs.oracle.com/javase/8/docs/api/)에 정의된 내용을 바탕으로 살펴보자.

###### Iterable

> Implementing this interface allows an object to be the target of the "foreach" statement.
> 
> 해당 인터페이스를 구현하면 **Foreach statement**의 대상이 될 수 있다. 

java docs의 설명처럼 Iterable은 해당 Object들을 순회할 방법을 제공한다. Collection에서 Iterator을 받아서 `.hasNext()`, `.next()`를 이용하면 된다.

인터페이스들의 상속 그림에서 보는 것처럼, 모든 Collection은 Iterable 인터페이스를 상속한다. 즉 Collection이라면 Iterator를 반드시 리턴한다고 볼 수 있다.

###### Collection

Iterable을 상속하는 Collection이다.

> A collection represents a group of objects, known as its elements. Some collections allow duplicate elements and others do not. Some are ordered and others unordered. The JDK does not provide any direct implementations of this interface: it provides implementations of more specific subinterfaces like Set and List. This interface is typically used to pass collections around and manipulate them where maximum generality is desired.
> 
> 컬렉션은 객체의 모임을 나타낸다. 일부 컬렉션들은 중복을 허용하고 , 다른 컬렉션은 허용하지 않는다. 정렬 또한 마찬가지다. JDK는 이런 인터페이스의 직접 구현을 제공하지 않고, Set과 List와 같은 보다 구체적인 하위 인터페이스의 구현을 제공한다. 
> 해당 인터페이스는 일반적으로 컬렉션들을 전달하거나, 최대한 추상화해 해야 하는 곳에서 사용된다.

위 문서에 잘 설명되어 있는 것 같다. 말그대로 Object의 모임을 표현하는 아주 추상화된 인터페이스다. 모여있다는 것에만 관심이 있고 어떻게 모여있는지에 대해서는 관심이 전혀 없다. 그래서 `.add()`, `.contains()`, `.isEmpty()`, `.remove()`등의 메소드를 통해  모여있는 Objects를 구성하는데 꼭 필요한 메소드를 지원한다.

###### List

다음은 Collection을 상속받는 List에 대한 설명이다.

> An ordered collection (also known as a sequence). The user of this interface has precise control over where in the list each element is inserted. The user can access elements by their integer index (position in the list), and search for elements in the list.
>
> 순서가 있는 컬렉션이다. 이를 사용하면 각 요소가 삽입되는 위치를 정확하게 제어할 수 있다. 정수의 인덱스로 요소에 접근할 수 있으며, 리스트에서 요소를 검색할 수도 있다.

위 설명에서 순서, 삽입되는 위치, 검색이 눈에 띈다. 이는 Collection에서는 구체화하지 않았던 **각 요소가 어떻게 모여있는지**에 해당한다. 그래서 List 인터페이스는 `.get()`, `.set()`,  `.indexOf()` 등의 메소드를 지원한다.

###### Queue

다음은 Collection을 상속받는 Queue에 대한 설명이다.

> A collection designed for holding elements prior to processing. Besides basic Collection operations, queues provide additional insertion, extraction, and inspection operations. 
>
> 여러요소를 처리전에 보관하기 위해 디자인된 Collection이다. 기본적인 Collection의 작업 외에도 큐는 추가적인 삽입, 추출 및 검사 작업을 제공한다.

보통 FIFO를 따르는 Queue로써 `.add()`, `.peek()`, `.poll()`등을 지원한다.

###### Set

다음은 Collection을 상속받는 Set에 대한 설명이다.

> A collection that contains no duplicate elements. As implied by its name, this interface models the mathematical set abstraction.
>
> 중복 요소가 없는 Collection이다. 이 인터페이스는 이름에서 알 수 있듯이 수학적 집합 추상을 모델링한다.

Collection을 상속받는 Set 인터페이스의 메소드 목록을 살펴보면 다른 것이 없다. 다만 Set을 구현하는 클래스에서 반드시 지켜야하는 사항이 있는데, 위에서 볼 수 있듯이 **중복되는 요소를 추가할 수 없는 것**이다. 수학에서 말하는 집합과 같은 의미로 생각하면 된다.

###### Map

>An object that maps keys to values. A map cannot contain duplicate keys; each key can map to at most one value.
>
>Keys를 Values에 매핑하는 객체다. Map에는 중복되는 키를 가질 수 없다.(각 키는 최대 하나의 값에만 매핑될 수 있다)

키와 값을 1:1로 보관하고 있는 객체다. 쉽게 말해서 key는 중복이 안 되는 Set, value는 collection이라고 생각하면 된다.

### 참고

[Java docs](https://docs.oracle.com/javase/8/docs/api/)

[자바 기초 공부 블로그](http://scarlett.tistory.com/entry/%EC%9E%90%EB%B0%94%EA%B3%B5%EB%B6%80-1%EC%9E%90%EB%B0%94%EC%BB%AC%EB%A0%89%EC%85%98-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC)

[Collection in Java and Collection Framework](http://www.dineshonjava.com/2013/05/java-collection-framework.html#.WLp0wW_yguU)

[컬렉션 프레임워크의 개념](http://tcpschool.com/java/java_collectionFramework_concept)