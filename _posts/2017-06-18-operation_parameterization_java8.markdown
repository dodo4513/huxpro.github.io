---
layout:     post
title:      "변화하는 요구사항에 대응하는 유연한 코드 - java8 Lambda"
subtitle:   "동작 파라미터화 코드를 전달하기"
date:       2017-6-18
author:     "Xavy"
catalog:    true
tags:
    - Java
    - Java In Action
---

### 시작하기 전에

 개발을 하다보면, 자바8 관련 소스를 많이 본다. 주로 Lambda와 Stream 관련 문법들인데 소스를 보고 있으면 기존의 것들과 비교해 정말 좋은 것 같다.
보통 List 데이터를 가지고 어떤 일을 할 때, 보통은 어떻게 순회하는 할지 보다는 하나하나의 데이터를 어떻게 처리할지가 더 중요한 관심사인데, Stream을 사용하면 아주 깔끔하게 처리할 수 있다. 

그래서 Java 8에서 추가된 것들을 공부하고 싶은데, 짧은 영어 실력으로 원문을 보기엔 아직은 불편해서 책을 찾아보게 되었다.

<img class="shadow" src="/img/my-post/book_image/java8_action.PNG" alt="java8">

위 책은 Java의 Lambda, Stream, Functional Programming과 관련된 책으로 내용이 좋은 것 같다.
책의 전반적인 내용을 내가 다시 볼 목적으로 관심있는 부분만 정리해 두려고 한다. 
더욱 자세히 공부하고 싶다면 책을 읽어보길 바란다! 해당 포스트의 내용은 `2장 동작 파라미터화 코드 전달하기`를 보면 된다.

- - -

###### Java in Action과 관련된 다른 포스팅들

1. [Java8을 눈여겨봐야하는 이유](https://dodo4513.github.io/2017/06/11/essential_java8/)

2. 변화하는 요구사항에 대응하는 유연한 코드 - java8 Lambda

3. [람다와 함수형 인터페이스](https://dodo4513.github.io/2017/06/25/lambda_1_java8/)

- - -

### 변화하는 요구사항에 대응하는 유연한 코드 작성하기

> 어떤 상황에서 일을 하든 소비자 요구사항은 항상 바뀐다. 시시각각 변하는 사용자의 요구사항에 어떻게 대응해야 할까?

- - -

###### 1. 동작이 아닌 값으로 요구사항을 전달 받아야 할때 나타나는 한계점

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if("green".equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}
```

위 소스는 녹색 사과만을 골라 List로 반환하고 있다. 만약 빨간 사과만을 골라서 리스트에 담는 메소드도 필요하다면 어떻게 해야할까?
무작정 위 소스를 복사해 `filterRedApples`로 만드는 사람은 없겠지? 반복되는 소스가 가져오는 문제점은 이미 잘 알고 있을 것이다.
  
```java
public static List<Apple> filterApplesByColor(List<Apple> inventory, String color) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(color.equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}
```
 
단순히 위처럼 color 값을 입력받도록 바꿔 `filterApplesByColor` 메소드를 만들었다. 파라미터로 변하는 요소를 추출해 보다 유연한 메소드가 된 것이다.

그런데 색상 뿐아니라 무게로도 필터링을 해야한다고 하면 어떻게 해야할까?

```java
public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(apple.getWeight > weight) {
            result.add(apple);
        }
    }
    return result;
}
```

위 처럼 따로 `filterApplesByWeight` 메소드를 만들 것인가? 그런데 잘 살펴보면 `filterApplesByColor` 메소드와 구성이 별반 다르지 않다.
조건을 판단하는 if 조건 구문만 약간 다를 뿐 나머지 코드는 거의 중복되는 코드다. 그렇다면 이 둘을 합쳐서 중복을 제거해보는건 어떨까?

```java
public static List<Apple> filterApplesByWeightAndColor(List<Apple> inventory, String color, int weight, boolean flag) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if((flag && apple.getWeight > weight) || (!flag && apple.getColor.equals(color))) {
            result.add(apple);
        }
    }
    return result;
}
```

이젠 위 메소드 하나로 색상 혹은 무게로 필터링할 수 있게 되었지만, 어떤 요구조건으로 필터링할지를 판단하는 `flag` 값이 추가 되었다.

조건이 단순히 2가지 뿐이라 **boolean flag** 로 판단할 수 있지만 만약 색상, 무게 뿐아니라, 원산지가 추가된다면? 
위 flag 형을 int나 enum으로 관리할 것인가? 심지어 여러 조건들을 모두 사용해 우선순위에 맞춰 필터링 해야한다면?

결국 위 처럼 파라미터를 통해 여러 요구사항을 받아들이는 것은 한계가 있다.
또한 위에서 살펴본 소스들은 if 구문을 제외하고는 거의 같다. 

**어떻게 필터링 할 것인지를 정하는 if 구문을 파라미터로 받을 수 있다면 아주 유연한 메소드를 만들 수 있지 않을까??**

- - -
 
###### 2. 전략 디자인 패턴을 이용해 유연성을 얻는 방법

<img class="shadow" src="/img/my-post/20170618_operation_parameterization_java8/predicateUML.PNG" alt="predicate_uml">

위처럼 Applepredicate 인터페이스를 통해 어떤 행동을 할지 입력을 받는다면 아까의 한계를 해결할 수 있지 않을까?

```java
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(p.test(apple)) {
            result.add(apple);
        }
    }
}
```

predicate 객체로 사과 검사 조건을 캡슐화함으로써, 보다 유연한 소스를 얻었다. 
이제 요구사항에 맞춰 다양한 ApplePredicate를 만들어서 위 filterApples로 전달하기만 하면된다. 예를 든다면 아래와 같다.

```java
public class AppleWeightPredicate implements ApplePredicate {
    public boolean test(Apple apple) {
        return apple.getWeight() > 150;
    }
}

List<Apple> heavyApples = filterApples(inventory, new AppleWeightPredicate());
```

어떤 Predicate 객체를 넘기느냐에 따라 filterApples의 메소드 동작이 달라진다. 즉 filterApples 메서드의 동작을 파라미터화 한 것이다.
이 구조는 **각 알고리즘을 캡슐화하는 클래스들을 미리 구현해둔 다음 런타임 시점에 알고리즘을 선택하는 전략 디자인 패턴** 이다.

- - -

###### 3. 익명 클래스를 이용해 더욱 간결하게 

위 전략 디자인 패턴은 ApplePredicate 인터페이스를 구현하는 여러 클래스를 정의한 다음에 인스턴스화해 사용해야 한다.
사실 때에 따라서 번거로운 작업이다. 

보다 간단하게 사용할 수 있는 방법도 있는데, 바로 **익명 클래스**를 사용 하는 것이다.
자바에서는 익명 클래스를 이용해 클래스의 선언과 동시에 인스턴스화를 동시에 수행할 수 있는 기법을 제공한다.

```java
List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return "red".equals(apple.getColor());
    }
});
```

작성해야할 소스가 전략 디자인 패턴을 이용할 때 보다 많이 줄었다. 
predicate 다시 사용해야할 일이 없을 때 간단하게 위처럼 구현해 바로 사용할 수 있다.
위 스타일을 보다 더 좋은 방법은 없을까? 자바8의 Lambda를 이용하면 위 소스를 더 간단하게 표현할 수 있다.

- - -

###### 4. Lambda 표현식 사용

```Java
public interface Predicate<T> {
    boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p){
    List<T> result = new ArrayList<>();
    for(T e: list) {
        if(p.test(e)) {
            result.add(e);
        }
    }
    return result;
}

List<Apple> redApples = filter(inventory, (Apple apple) -> "red".equals(apple.getCollor()));
List<Apple> heavyApples = filter(inventory, (Apple apple) -> apple.getWeight > 150);
```

형식 파라미터<T>가 등장해 익숙하지 않으면 조금 어려워 보일 수 있으나 찬찬히 잘 살펴보면 기존의 소스와 크게 다르지 않다.

먼저, 익명 클래스를 사용한 소스 예제와 위 소스들을 비교해 보자

익명클래스를 사용하면 **ApplePredicate를 인스턴스화 하고 public boolean test()** 를 재정의 해야하는데, 이에 따른 불필요한 소스가 존재한다.
사실 우리에게 관심이 있는것은 어떻게 필터링 할까를 정의한 `return "red".equals(apple.getColor());` 이 부분이다. 
위 동작만 알려주면 될 뿐 인스턴스화와 재정의는 로직상 의미가 없는 문법적인 요소일 뿐이다.

```Java
List<Apple> redApples = filter(inventory, (Apple apple) -> "red".equals(apple.getCollor()));
```

반면에 자바8의 Lambda를 이용한다면, 아주 간단하게 어떻게 필터가 동작해야하는지만을 간단하게 넘겨줄 수 있다.
아주 소스가 간결해 졌고, 누가봐도 위 소스는 빨간색 사과만을 가져오는 로직이란걸 한눈에 알아볼 수 있다.

- - -

### 마치며

메소드의 파라미터에 모든 필요한 속성을 나열하는 것 보단 전략 디자인 패턴, 익명 클래스, Lambda를 이용해 보다 유연한 메소드를 설계 하는 방법에 대해 살펴봤다.
여러 방법들의 공통점은 동작 파라미터화를 통해 유연한 메소드를 설계하는 것이다.

개인적으로 그 중 아주 간결한 소스와 개발 의도를 명확히 표현하는 Lambda 표현식이 인상깊다. 
Lamda에 대한 자세한 문법은 나중에 알아보기로 하고 해당 포스트는 이것으로 끝! 

- - -

### 참고

[자바 8 인 액션 - yes24](http://book.naver.com/bookdb/book_detail.nhn?bid=8883567)