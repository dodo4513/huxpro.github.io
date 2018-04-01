---
layout:     post
title:      "Spring boot H2 DB DAO 예제"
subtitle:   "토비의 스프링 1.1장 초난감 DAO 구현하기"
date:       2018-04-01
author:     "Xavy"
catalog:    true
tags:
    - Spring
---

### 토비 스프링 3.1 예제 따라 하기와 관련된 다른 포스팅들

토비 책을 읽으며 나오는 소스들을 직접 하나하나 세팅 및 구현해 보려는 포스트들입니다. 
꾸준히 할 수 있을지는 모르겠지만 우선 시작!

1. [Intellij Spring boot Hello world 예제](https://dodo4513.github.io/2018/02/03/spring_hello_world/)

2. [Spring boot H2 DB 예제](https://dodo4513.github.io/2018/02/11/spring_h2/)

3. [Spring boot H2 DB DAO 예제](https://dodo4513.github.io/2018/04/01/spring_h2_init/)

위 포스팅의 결과물들은 아래의 깃에서 각 브랜치로 관리하고 있습니다!

[https://github.com/dodo4513/xavyspring](https://github.com/dodo4513/xavyspring)

- - -

### 시작하기 전에

해당 시리즈의 마지막 포스트는 2.11일에 작성되었고 약 한 달반 만에 다음 진도를 나가게 됐다.
미리 작성해둔 entity의 ddl create 해주는 부분이 이상하게 안되더라.

그래서 자괴감에 빠져 허송세월 보내다 오늘 다시해보니 web console의 jdbc 주소를 잘못 써서 테이블이 안보이는 문제였다 ㅜㅜ 
자세한 내용은 아래에서 다시 언급하겠다. 다시 시작!

**지난 [Spring boot H2 DB 시작 예제](https://dodo4513.github.io/2018/02/11/spring_h2/)의 소스 기반으로 이어서 개발한다.**
 
- IntelliJ IDEA 2017.1.5
- JRE: 1.8.0_112-release-736-b21 amd64
- JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o
- Windows 10 10.0
- **spring boot 1.5.10.RELEASE**

- - -

### 본 예제와 관련된 토비의 스프링 목차

토비의 스프링(54p ~ 59p)
    1장 오브젝트와 의존관계
        1. 초난감 DAO
            1. User
            2. UserDao
            3. main()을 이용한 DAO 테스트 코드

- - -

### 1. Entity로 User 테이블 자동 생성하기

```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
```
Jpa의 entity를 사용하기 위해서는 **pom.xml**에 위 소스를 추가한다.

<img class="shadow" src="/img/my-post/20180401_spring_db_init/1.model_class.JPG" alt="init">

그리고 난 model 패키지 아래에 User class를 생성하자.

이제 모델을 정의해야 하는데 이는 책 55p에 보면 Users의 명세가 있다.

|필드명|타입|설정|
|---
|Id|VARCHAR(10)|Primary Key|
|Name|VARCHAR(20)|Not Null|
|Password|VARCHAR(20)|Not Null|

요렇게 생긴 Users 테이블 구성을 바탕으로 Entity class를 정의하면 아래와 같다.

```java
package study.model;

import javax.persistence.*;

@Entity
@Table(name = "Users")
public class User {

    @Id
    @Column(length = 10)
    String id;

    @Column(length = 20, nullable = false)
    String name;

    @Column(length = 20, nullable = false)
    String password;
}
```

 - @Entity : 해당 class는 entity임을 선언한다.
 - @Table(name = "") : name 속성으로 table 이름을 지정해줄 수 있다. 테이블 명은 Users고 그 각각의 데이터를 담을 entity는 User로 명시했다.
 - @Id : 해당 컬럼은 기본키
 - @Column : 컬럼의 속성을 정의한다. 위에서는 최대 길이와 Not null을 명시.

그리고 위 model 객체는 getter와 setter가 필요하다. 
책에서 처럼 각각 나열해 줄 수도 있지만, **getter와 setter는 값을 꺼내오고, 값을 할당하는 데 의미가 있지 어떻게 그 소스를 작성하는지는 중요하지 않다.** 

때문에 보통 lombok 라이브러리를 사용한다. 역시나 **pom.xml**에 아래를 추가한다.

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

그리고 아래처럼 Class 위에 **@Getter, @Setter**을 붙여주면 된다.

<img class="shadow" src="/img/my-post/20180401_spring_db_init/2.get_set.jpg" alt="get_set">

그리고 h2 web console에 접속해보자.

<img class="shadow" src="/img/my-post/20180401_spring_db_init/3.console.JPG" alt="console">

**단 저 JDBC url을 꼭 확인하자 ㅜㅜ 1달 넘게 저 주소를 잘못 입력한걸 모르고 삽질만 했다..**   

<img class="shadow" src="/img/my-post/20180401_spring_db_init/4.console_result.JPG" alt="console_result">

위와 같이 빌드 후 console을 확인 했을 때 테이블이 생성되 있다면 성공!

- - -

### 2. UserDao 만들기

테이블을 만들었으니, UserDao를 만들어보자.

<img class="shadow" src="/img/my-post/20180401_spring_db_init/5.dao.JPG" alt="console_result">

56p ~ 57p에 소스가 나와있으니 따라하는건 어렵지 않다. **Class.forName만 h2 driver로 변경**해주자.

```java
package study.Dao;

import study.model.User;

import java.sql.*;

public class UserDao {

    public void add(User user) throws ClassNotFoundException, SQLException {
        Class.forName("org.h2.Driver");
        Connection c = DriverManager.getConnection("jdbc:h2:mem:testdb", "sa", "");
        PreparedStatement ps = c.prepareStatement("INSERT INTO users(id, name, password) values(?, ?, ?)");
        ps.setString(1, user.getId());
        ps.setString(2, user.getName());
        ps.setString(3, user.getPassword());

        ps.executeUpdate();

        ps.close();
        c.close();
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Class.forName("org.h2.Driver");
        Connection c = DriverManager.getConnection("jdbc:h2:mem:testdb", "sa", "");
        PreparedStatement ps = c.prepareStatement("SELECT * FROM users WHERE id = ?");
        ps.setString(1, id);

        ResultSet rs = ps.executeQuery();
        rs.next();
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));

        rs.close();
        ps.close();
        c.close();

        return user;
    }
}

```

- - -

### 3. Main()을 이용한 DAO 테스트 코드

스프링에서 main()은 예전에 같이 살펴봤듯이 Application.class에 있다.

그 안에 58p ~ 59p의 소스를 그대로 넣어주면 된다.

```java
package study;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import study.Dao.UserDao;
import study.model.User;

import java.sql.SQLException;

@SpringBootApplication
public class Application {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        SpringApplication.run(Application.class, args);

        UserDao dao = new UserDao();
        User user = new User();
        user.setId("whiteship");
        user.setName("백기선");
        user.setPassword("married");

        dao.add(user);

        System.out.println(user.getId() + "등록 성공");

        User user2 = dao.get(user.getId());
        System.out.println(user2.getName());
        System.out.println(user2.getPassword());

        System.out.println(user2.getId() + " 조회 성공");
    }
}
```  

그리고 서버 재시작!

<img class="shadow" src="/img/my-post/20180401_spring_db_init/6.sysout.JPG" alt="success">  

- - -

### 마치며

아주 간단한 주제로 오랫동안 삽질한게 너무 부끄럽다...

<img class="shadow" src="/img/my-post/20180401_spring_db_init/7.etc.jpg" alt="success">

조금 변명을 하자면, 위 소스를 빌드하면 빌드 로그 중에 저 **HHH000206: hibernate.properties not found**가 있다.
저거에 낚여서 무언가 세팅이 안되서 table이 자동생성이 안되는 문제로 착각하고 오만가지 세팅을 다 해봤다...

그러다 불현들 JDBC의 url에서 설마 잘못된건 아닌가 생각했고, 스프링 default url 값을 넣으니 잘 되더라 ㅜㅜㅜ

위 소스는 아래의 Git에 올려두었습니다. **03_h2_dao 브랜치를 참고**하시면 됩니다.

[https://github.com/dodo4513/xavyspring/tree/03_h2_dao](https://github.com/dodo4513/xavyspring/tree/03_h2_dao)

잘못된 점이나 모호한 설명이 있다면, 편하게 메일 주세요~ 적극적으로 반영하도록 하겠습니다!

- - -

### 참고

[spring boot default H2 jdbc connection (and H2 console)](https://stackoverflow.com/questions/24655684/spring-boot-default-h2-jdbc-connection-and-h2-console)