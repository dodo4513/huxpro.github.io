---
layout:     post
title:      "Intellij 아주 간단한 Spring boot - Hello world 예제"
subtitle:   "Maven 템플릿으로 spring 설정해보자"
date:       2018-2-3
author:     "Xavy"
catalog:    true
tags:
    - Spring
---

### 시작하기 전에

 2017년 한해가 끝났는데, 내 실력은 오히려 더 하락한 것 같다ㅜㅜ.. 그래서 새로운 마음으로 토비책을 펼쳤다.
1장 예제부터 손수 구축해 보려고 하는데 그 중 첫 번째 시리즈가 되지 싶다. 초난감 DAO부터 시작하는데 Spring 초가 세팅부터 아주 고역이다.
  
항상 누군가 이미 다 구축해논 환경에서 개발하다보니, 새로운 스프링 프로젝트를 만들때면 언제나 헤메게 되는 것 같다. 이번참에 정리해보자

- IntelliJ IDEA 2017.1.5
- JRE: 1.8.0_112-release-736-b21 amd64
- JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o
- Windows 10 10.0
- **spring boot 1.5.10.RELEASE**

- - -

### Hello world!



###### 1. Maven Project 생성
   
Spring initializer나 Spring project로 만들어서 maven 프로젝트로 만들 수도 있지만, spring 환경을 손으로 구축해 보는 것도 의미가 있겠다 싶어 Maven으로 시작.

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/1_make_maven" alt="maven">
 
적당히 입력해 만들면, 아래와 같이 텅텅빈 디렉토리 구조를 가진 프로젝트가 생성된다.

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/2_make_maven_after" alt="maven">

- - -

###### 2. pon.xml에 Spring 추가

```xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>study.spring</groupId>
    <artifactId>xavy-spring</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.10.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

spring boot의 상위 parent tag와 변수가 모여있는 properties, spring-boot-starter-web dependency, 마지막으로 spring boot maven 플러그인을 추가한다.

그리고 maven 탭에서 reimort all maven projects를 누르면 프로젝트에 적용이 된다. 

- - -

###### 3. Application.java 생성

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/3_directory_structure" alt="maven">

위 모양으로 Application과 HelloController를 만들어 줄 것이다. 먼저 Application.java를 만들어보자

```java

package study;

import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

```

spring boot도 여느 자바 프로그램과 같이 `public static void main(String[] srgs)`가 필요하다. 
그 역할을 하는 친구인데, 클래스 이름이 꼭 Application을 사용할 필요는 없지만 보편적으로 위 이름이 사용된다.

유의 깊게 볼 부분은 `@SpringBootApplication`인데, spring 개발자들이 주로 사용하는 **@Configuration**, **@EnableAutoConfiguration**, **@ComponentScan**을 한데 모아둔 것이라고 한다.

[Spring docs - Using the @SpringBootApplication annotation] 

각각의 어노테이션을 살펴보며, 재밌던 부분은 @ComponentScan 이다.
 
```xml

<context:component-scan base-package="com.example" />

```

spring xml 설정에서 자주 보던 코드였는데, 해당 기능을 하는 어노테이션이라고 한다. 
따라서 spring은 Application.java를 위치를 기준으로 하위 클래스들을 스캔해 빈으로 등록한다.

Application.java의 위치는 잘 보면 java.study 밑에 있는데, 만약 java 밑에 바로 두면 위치가 잘못된게 아니냐고 에러가 나더라. 
Root 위치가(java) 아닌 바로 아래의 디렉토리에(java.study) 있어야 한다.

- - -

###### 4. HelloController 생성

```java

package study.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/")
    public String helloWorld() {
        return "hello, My name is xavy";
    }
}

```

아주 심플하다. 단순히 문자열을 리턴할 생각이므로 `@RestController` 를 붙였다.
`@GetMapping` 붙여주고 아주 간단하게 끝.

###### 5. 실행화면 

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/4_browser" alt="maven">

위 까지 세팅하고 돌려보면 원하는 메시지가 아주 잘 뜨는 걸 볼 수 있다.

Spring Boot 프로젝트기 떄문에 web.xml 이니 servlet-context.xml 같은 설정파일은 물론 톰캣 설정 또한 필요없기 떄문에 아주 간편하다.


###### 6. web.xml 생성

앞으로 프로젝트에서 Java config 를 적극적으로 사용할 생각이라 필요없을 것 같지만, 혹시나 하는 마음에 만들어보기로 했다.

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/5_webxml" alt="maven">

Project Structure 를 켜서 빨간색 네모를 참고해 추가하면 끝이다. 다만 path 설정이 요상한게 들어있을 텐데 적당히 바꿔준다. 

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/6_path" alt="maven">

그리고 아래에 web resource directory 도 설정해주자

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/7_web_resource_directory" alt="maven">

아래와 같이 추가되면 끝.

<img class="shadow" src="/img/my-post/20180203_spring_hello_world/8_web_resource_directory_after" alt="maven">

- - -

### 마치며

처음엔 토비님이 책과 함께 제공하는 소스를 import 할까 했는데, 처음 세팅부터 해보는 것도 스프링 공부에 도움이 되겠다 싶어 시작했는데 시간이 너무나도 오래 걸렸다.

그래도 이것저것 알게된게 많아 한 번쯤은 꼭 해보면 좋을 것 같다. 위 소스는 아래의 Git에 올려두었습니다.

[https://github.com/dodo4513/xavyspring/tree/01_plain_spring](https://github.com/dodo4513/xavyspring/tree/01_plain_spring)

잘못된 점이나 애매한 설명이 있다면, 편하게 메일 주세요~ 수정하도록 하겠습니다.

- - -

### 참고

[3. Application 기본 구조 및 실행](http://www.libqa.com/wiki/728)

[intellij spring mvc 설정](http://multifrontgarden.tistory.com/108)

[Spring mvc (2) 그리고 Spring boot](http://wonwoo.ml/index.php/post/1590)
