---
layout:     post
title:      "무중단 배포(Zero downtime deployment) 하기"
subtitle:   "L4, Nginx, Tomcat, Spring 환경에서 무중단 배포를 해보자"
date:       2017-4-12
author:     "Xavy"
catalog:    true
tags:
    - Infra
---

### 시작하기 전에

 잘 이용하던 웹 사이트가 갑자기 에러를 내뿜을 때, 그걸 이해해 줄 수 있는 사람은 그걸 개발한 개발자 밖에 없다. 어떤 상황에서도 서비스는 잘 돌아가야 한다. 당연히 새로운 버전으로 배포를 하는 경우에도 마찬가지. 하지만 기본적으로 소스가 변경되면 서버를 다시 빌드를 해야하고 그 사이에 접근하는 사용자들에게는 에러 페이지를 보여줄 수 밖에 없다. 그렇다면 어떻게 해야할까?

 리얼 서비스를 위한 인프라 서버들을 구축하며 위와 같은 이슈를 만났고, 아래의 방법들로 해결을 시도했다.

###### 서버 환경


### 여러 방법들

###### 첫 번째, Static 변수 활용

###### 두 번째, Jsp 파일 활용

###### 세 번쨰, Tomcat Parallel Deployment 사용

### 마치며

### 참고
[tomcat parallel deployment - apache docs](https://tomcat.apache.org/tomcat-7.0-doc/config/context.html#Parallel_deployment)