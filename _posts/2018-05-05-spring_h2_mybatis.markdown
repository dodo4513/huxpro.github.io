---
layout:     post
title:      "Spring boot H2 Mybatis 예제"
subtitle:   "H2 + Mybatis 간단 예제"
date:       2018-05-05
author:     "Xavy"
catalog:    true
tags:
    - Spring
---

### Spring boot 공부하기 관련 포스팅들

Spring Boot로 간단한 홈페이지를 만들겁니다. 
간단한 게시판부터 로그인까지, 소스 복붙이 아닌 한줄한줄 공부하며 진행하려고 하는데요.
이미 벌려놓은 일이 많아 어떻게 될 진 모르겠지만, 여튼 출발!

1. []

위 포스팅의 결과물들은 아래의 깃에서 각 브랜치로 관리하고 있습니다!

[https://github.com/dodo4513/xavyspring](https://github.com/dodo4513/xavyspring)

- - -

### 시작하기 전에

굳이 다른 DB 설치할 것 없이 Spring boot는 H2 라고하는 In-memory 타입의 내장 DB를 지원한다. 
spring을 실행하는 것만으로도 DB를 함께 켜주니 간단히 사용하기 정말 편리하다.
오늘의 목표는 H2에 Mybatis를 붙여 select 질의를 해보는 것이다.

H2 설치는 [Spring boot H2 DB 예제](https://dodo4513.github.io/2018/02/11/spring_h2/)에 가면 설명이 되어 있고 해당 소스를 이어받아 Mybatis를 세팅해 볼 예정입니다. 

**위 포스트 혹은 [Hello word + H2가 세팅된 Git branch - A00_init](https://github.com/dodo4513/xavyspring/tree/A00_init) 소스를 기반으로 따라하시면 됩니다.**
 
- IntelliJ IDEA 2017.1.5
- JRE: 1.8.0_112-release-736-b21 amd64
- JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o
- Windows 10 10.0
- **spring boot 1.5.10.RELEASE**

- - -

### Mybatis, H2를 이용해 데이터 Select 예제


마지막 결과 디렉토리 모습 찍어올려!

###### 1. 엔티티 정의하기

Mybatis를 세팅하기 앞서, 아주 간단한 엔티티 클래스를 만들자.

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/0.model.JPG" alt="model">

```java
@Setter
@Getter
public class Member {
    private long no;
    private String name;
}
```

* @Getter, @Setter는 Lombok의 어노테이션이다.

###### 2. 테이블 정의 및 데이터 삽입하기

[78.3 Initialize a database - Spring docs](https://docs.spring.io/spring-boot/docs/1.5.13.BUILD-SNAPSHOT/reference/htmlsingle/#howto-initialize-a-database-using-spring-jdbc)의 첫줄을 읽어보자.

> Spring Boot can automatically create the schema (DDL scripts) of your `DataSource` and initialize it (DML scripts): it loads SQL from the standard root classpath locations `schema.sql` and `data.sql`, respectively

위 스프링 문서를 살펴보면 첫 줄에 정말 중요한 내용이 나온다.
Spring boot는 자동으로 DataSource의 스키마를 생성하고 초기화해주는데, root classpath 경로에 `schema.sql`, `data.sql`을 각각 로드한다고 한다.

우리의 소스는 H2를 이미 설치했기 때문에 boot가 알아서 DataSource도 빈으로 넣어 줄것이고 우린 저 schema.sql과 data.sql만 넣어주면 된다.

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/1.sqlfile.PNG" alt="sql">

아래는 schema.sql에 들어갈 내용.
```sql
CREATE TABLE member
(
  no BIGINT AUTO_INCREMENT PRIMARY KEY NOT NULL,
  name VARCHAR2(10) NOT NULL
);
```

이는 data.sql에 들어가야할 내용.
```sql
INSERT INTO member(name) VALUES ('싸비1');
INSERT INTO member(name) VALUES ('싸비2');
```

요기까지 하고 빌드해보면 콘솔에 아래처럼 저 SQL 파일들을 로드했다고 로그가 남는걸 볼 수 있을 것이다.

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/2.sqlload.PNG" alt="load">

실제로 h2 웹 콘솔에 들어가면 볼 수 있다.

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/3.h2.PNG" alt="h2">

###### 3. Mybatis dependency 추가하기

H2 DB 준비는 끝났으니 Mybatis를 설치하자.

```xml
    <!-- TODO 1 mybatis-spring-boot-start 추가-->
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.3.2</version>
    </dependency>
```

먼저 **pom.xml**에 위 dependency를 추가한다. 
해당 artifact는 IDE의 도움을 받아 내부를 살펴보면 **아래와 같은 dependency**를 들고 있는데 

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</gr*__*oupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-autoconfigure</artifactId>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
    </dependency>
```

mybatis는 물론 jdbc부터 boot artifact까지 다 들고 있으므로 이것저것 넣어줄 필요가 없다.
깔끔한 pom.xml 관리를 위해 항상 어떤 dependency를 가지고 있는지 확인해 보는 습관을 들이자.

###### 4. MemberMapper 인터페이스 생성하기

이제 Mybatis를 이용한 DAO 계층의 Mapper 인터페이스를 만들자.

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/4.mapper.PNG" alt="mapper">

```java
@Mapper
public interface MemberMapper {

    @Select("SELECT * FROM member WhERE no = #{no}")
    Member selectMember(long no);

}
```

mybatis의 @Mapper가 붙은 인터페이스는 따로 구현 클래스를 만들지 않아도 알아서 Bean으로 만들어준다.
따라서 간단히 위와 같이 선언만 하면 그 내부 구현은 mybatis가 알아서 한다. 

보통 Service 계층을 두고 Controller와 Repository가 서로 통신을 하겠지만, Service에 들어갈 것도 딱히 없고 당장 중요한 포인트도 아니니 Service 계층은 생략하겠다.

아래는 기존에 있던 HelloController를 조금 수정한 소스다.

```java
@RestController
public class HelloController {

    @Autowired
    MemberMapper memberMapper;

    @GetMapping("/")
    public String helloWorld() {
        return "hello, My name is xavy";
    }

    @GetMapping("/members/{no}")
    public Member helloWorld(@PathVariable("no") long no) {
        return memberMapper.selectMember(no);
    }
}
``` 

memberMapper를 주입받아 REST 호출이 들어오면 그에 맞춰 selectMember(no)를 호출하는 매우 간단한 코드다. 

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/5.result.PNG" alt="mapper">

복잡한 xml 설정 하나 없이 annotation으로 정말 간단하게 mybatis를 연동했다.

그러나 위 코드는 조금 고민해볼 거리가 있는데, MemberMapper는 SQL 코드와 Java 코드가 함께 혼재되어 있다. 
위 예제의 SELECT 문은 워낙 간단해 당장은 가독성이 나쁘지 않지만, 쿼리가 복잡해지거나 다양해짐에 따라 관리가 힘들어질 것이다.   

###### 5. SQL문 따로 나누기

root classpath 아래에 적당한 디렉토리를 만들고 그 아래에 member.xml을 생성했다.

<img class="shadow" src="/img/my-post/20180505_spring_h2_mybatis/6.memberxml.PNG" alt="memberXml">

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="study.dao.MemberMapper">
    <select id="selectMember2" resultType="study.model.entity.Member">
        select * from member where no = #{no}
    </select>
</mapper>
```

아까 memberMapper에 있던 쿼리를 위와 같은 형식으로 옮겼다.

* 위 resultType에 path를 나열하지 않고 간단하게 줄이려면 @Alias 어노테이션과 mybatis.type-aliases-package을 살펴보면 될 것이다.

그리고 select태그의 id 값과 memberMapper 인터페이스의 메소드 이름을 아래와 같이 맞춰주면 된다.

```java
@Mapper
public interface MemberMapper {

    @Select("SELECT * FROM member WhERE no = #{no}")
    Member selectMember(long no);

    // 쿼리를 분리한 메소드
    Member selectMember2(long no);
}
``` 

controller에서 selectMember2를 호출해도 같은 결과를 볼 수 있을 것이다.

### 마치며

mybatis 세팅은 참 다양한 방법이 있는 것 같다. 
이곳 저곳 살펴보며 세팅할땐 참 힘들었는데, 다 알고 나니 굉장히 쉽다. 

레퍼런스를 항상 확인하며 개발하자.  

위 소스는 아래의 Git에 올려두었습니다. 아래 브랜치를 확인해주세요~
* 시작 브랜치 : A00_init
* 완성 브랜치 : A01_H2_Mybatis

잘못된 점이나 모호한 설명이 있다면, 편하게 메일 주세요~ 적극적으로 반영하도록 하겠습니다!

- - -

### 참고

[mybatis-spring-boot-autoconfigure](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)