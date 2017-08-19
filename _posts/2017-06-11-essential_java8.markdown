---
layout:     post
title:      "Java 8을 눈여겨봐야 하는 이유"
subtitle:   "자바 8 인 액션"
date:       2017-6-11
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
더욱 자세히 공부하고 싶다면 책을 읽어보길 바란다! 해당 포스트의 내용은 `1장 Java8을 눈여겨봐야하는 이유 35p`를 보면 된다.

- - -

###### Java in Action과 관련된 다른 포스팅들

1. Java8을 눈여겨봐야하는 이유

2. [변화하는 요구사항에 대응하는 유연한 코드 - java8 Lambda](https://dodo4513.github.io/2017/06/18/operation_parameterization_java8/)

3. [람다와 함수형 인터페이스](https://dodo4513.github.io/2017/06/25/lambda_1_java8/)

- - -

### Java 8을 구성하는 핵심적인 사항

###### 간결한 코드

아래는 기존 Java의 inventory의 사과들을 무개에 따라 정렬하는 코드다.
Comparator를 익명함수로 구현해 어떻게 정렬할지를 파라미터로 넘겨줘 inventory를 정렬한다.

```java
Collections.sort(inventory, new Comparator<Apple>(){
   public int compare(Apple a1, Apple a2) {
       return a1.getWeight().compareTo(a2.getWeight());
   } 
});
```
다음은 Java 8의 Lambda와 Method reference를 사용한 코드다. Comparator을 생성하는 부분이 사라지고 소스가 더욱 간결해졌다.

```java
//Lambda
inventory.sort(
    (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())
);
```

```java
//Method Reference
inventory.sort(comparing(Apple::getWeight));
```

처음에는 Java 8의 소스가 기괴하게 느껴졌는데, 알면 알수록 직관적이고 간결한 표현인것 같다.
한번 알고나니 이제는 아래 버전으로는 프로젝트를 할 수 없을 것만 같다.  

- - -
 
###### Multi-core processor의 간단한 활용

두 개의 실행 흐름, 성능 향상 등을 위해선 Thread를 고려해볼 수 있다. 하지만 Thread를 사용하면 별의 별 골치아픈 문제가 발생한다.
동시에 실행될 가능성이 있는 로직은 Stateless(무상태) 방식이 되던지, 락을 통해 스레드 간의 순서를 제어해줘야한다.
자바는 아래의 표와 같이 이러한 병렬 실행 환경을 쉽게 관리할 수 있고 에러가 덜 발생할 수 있는 방향으로 진화하려 노력했다.

| Java version | Supported content |
| 1.0 | Thread, Lock, Memory model |
| 5 | Thread pool, Concurrent Collection |
| 7 | Fork/Join Framework | 

Java 8에서도 새롭고 단순한 방법으로 병렬 실행을 새로운 Stream API를 제공한다. 
마치 DB의 SQL을 작성하는 것 처럼 고수준의 언어로 동작을 기술하면 최적의 저수준 실행 방법으로 최적화해 실행되는 방식으로 동작한다.
물론 몇 가지 규칙은 지켜야하긴 하는데, 무겁고 에러를 자주 일으키는 Syncronized 키워드를 사용하는 것 보단 좋을 것이다. 

좀더 자세한 내용은 Stream을 살펴보자.

- - -

### 참고

[자바 8 인 액션 - yes24](http://book.naver.com/bookdb/book_detail.nhn?bid=8883567)