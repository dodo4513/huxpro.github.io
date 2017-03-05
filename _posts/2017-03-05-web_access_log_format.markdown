---
layout:     post
title:      "Web Access Log Format"
subtitle:   "Log Format - common, Combined"
date:       2017-03-05
author:     "Xavy"
catalog:    true
tags:
    - Log
---

### 시작하기 전에

###### Access Log란?

Web Log는 모든 웹 서버에 공통적으로 제공되는 기능이다. 그 중 Access Log는 서버가 처리하는 모든 요청을 기록한다. 이 정보를 분석하면 유용한 통계자료를 만들어낼 수 있을 것이다. 일반적으로 Web 서버는 Access Log를 저장할 뿐 분석의 역할은 맡지 않는다.

###### Common Log Format

Access Log의 전형적인 Format이다. 아래의 예제를 바탕으로 하나씩 알아보자. 

```text
127.0.0.1 - frank [10/Oct/2000:13:55:36 -0700] "GET /apache_pb.gif HTTP/1.0" 200 2326
```

`127.0.0.1` : 서버에 요청한 클라이언트의 **IP 주소**이다.
`-` : 클라이언트의 RFC1413 identity를 나타낸다. 해당 예제는 - 기호를 사용해 요청한 정보가 없을을 나타낸다.(참고로 해당 항목은 Apache에서 해당 정보는 매우 믿을 수 없기 때문에 긴밀히 관리되는 내부 네트워크가 아니라면 절대로 사용하지 않길 권하고 있다.)
`frank` : HTTP 인증으로 알아낸 문서를 요청한 사용자의 userid이다.
`[10/Oct/2000:13:55:36 -0700]` : 서버가 **요청처리를 마친 시간**이다. 해당 포멧은 [day/month/year:hour:minute:second zone]이다.
`"GET /apache_pb.gif HTTP/1.0"` : **클라이언트의 요청 줄**이다. 요청한 메서드, 자원, 프로토콜을 순서대로 의미한다.
`200` : 서버가 클라이언트에게 보내는 상태코드다.
`2326` : 마지막 항목은 응답 헤더를 제외하고 클라이언트에게 보내는 **내용의 크기**를 의미한다.

### 참고

아래의 Apache Log reference에는 위와 관련된 읽을거리가 풍부하다!
[Apache Log file](https://httpd.apache.org/docs/2.4/logs.html)

[Common Log Format](https://en.wikipedia.org/wiki/Common_Log_Format)