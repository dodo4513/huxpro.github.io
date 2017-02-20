---
layout:     post
title:      "Session Clustering"
subtitle:   "한 대의 Web server가 다수로 늘어날 때 Session 처리는 어떻게 해야할까?"
date:       2017-2-20
author:     "Xavy"
catalog:    true
tags:
    - Web
---

### 시작하기 전에

###### Session의 작동 방식

<img class="shadow" src="/img/my-post/3_session_clustering/what_is_session.PNG" >

1. 먼저 **Client**의 첫 번쨰 Request에서 **Web Container**는 고유 Session ID를 생성하고 이를 Response와 함께 **Client**에게 돌려준다. 이는 **Web Container**에 의해 생성된 임시 Session이다.

2. **Web Container**가 요청의 출처를 식별할 수 있도록, **Client**는 각 요청과 함께 Session ID를 다시 전송한다.

3. **Web Container**는 이 ID와 일치하는 Session ID를 찾고, Request에 Session을 연결한다.

- - -

###### Session과 여러 대의 Web 서버

Stateless한 HTTP 프로토콜 특징 덕분에, 페이지 이동 간에 로그인과 같은 서비스를 제공하려면 Session을 사용해 구현하면 된다. 이 때 한 대의 Web 서버라면 무리없이 보통의 HTTPServletSession를 이용해서 로그인을 구현할 수 있지만, Web 서버가 2대 이상으로 구성된 상황에서도 올바르게 동작할까??

<img class="shadow" src="/img/my-post/3_session_clustering/load_balancer.PNG" >

Session 작동 방식 그림에서 볼 수 있듯, Client와 각 Server간에 관계를 맺는 것 이기 때문에, 기본적으로 모든 Web Server의 Session은 각 서버들 끼리 동기화되지 않는다. 따라서 각각 보관되기 때문에 Web Server1에서 로그인을 했는데, 다른 페이지를 Web Server2 요청한다면, Session이 끊어져 로그인이 풀리게 된다.

### Session Clustering

위와 같은 문제를 해결하기 위한 방법은 아래와 같이 여러가지가 있다.

1. Tomcat(or jboss) session clustering
2. Memcached session manager
3. Sticky Session
4. 암호화된 Cookie 이용


### 참고
[What is HttpSession?](http://www.studytonight.com/servlet/httpsession.php)
[Session Clustering에 대한 Groups 토론](https://groups.google.com/forum/#!topic/ksug/6ZA6hDJOdKA)
[Tomcat Session Cluster 관련 블로그](http://sarc.io/index.php/tomcat/111-tomcat-session-cluster-1)

