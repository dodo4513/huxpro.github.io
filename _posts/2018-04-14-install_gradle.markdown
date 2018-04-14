---
layout:     post
title:      "Install Gradle"
subtitle:   "그래들 설치하기"
date:       2018-04-14
author:     "Xavy"
catalog:    true
tags:
    - Gradle
    - translation 
---

### Gradle docs 번역 시리즈

Gradle 같은 빌드 툴은 한번 세팅해 두면, 수정해야 할 일이 드물다. 
그래서 나처럼 프로젝트 중간에 투입이 되면, 일하면서 gradle과 크게 부딪힐 일이 없기 때문에 Gradle에 대해 잘 모르고 쓰게되는 것 같다.

그래서 영어 공부도 하고 Gradle도 좀 더 자세히 알아볼 겸 공식 문서를 번역하려고 한다.
범위는 Install Gradle과 Get Started Guides!

1. Install Gradle
    1. [Install Gradle](https://dodo4513.github.io/2018/04/14/install_gradle/)


- - -

### 시작하기 전에

해당 포스트의 내용은 아래의 링크로 가시면 원문으로 볼 수 있습니다.

[Gradle Build Tool - Installation](https://gradle.org/install/)

gradle 메인에서 가장 첫 번째 코너인 1. Install Gradle에 관한 포스팅입니다.

레퍼런스를 번역하는 건 처음이라 시행착오가 많을 것 같지만, 우선 시작!

- - -

### 도움이 되는 정보

* [free live training](https://gradle.org/training/)에 가시면, 격월로 Gradle의 핵심 개발자와 함께하는 무료 강의를 등록할 수 있습니다.
* 우리의 [guides](https://gradle.org/guides/)와 [reference documentation](https://docs.gradle.org/current/userguide/userguide.html)은 훌륭한 읽을거리 입니다.
* [build scans](https://scans.gradle.com/get-started)를 사용하면, 빌드를 시각화 및 디버깅할 수 있습니다.
* [The Gradle Newsletter](https://newsletter.gradle.com/)를 구독하는건, 매월 발생하는 이슈를 확인하고 최신을 유지하는데 좋은 방법입니다. 
* [Command-line completion](https://github.com/gradle/gradle-completion) 스크립트를 이용하면 bash, zsh에서 자동완성 기능을 사용할 수 있습니다.

gradle은 널리 이용되는 OS라면 무리 없이 실행이 가능합니다. 단, [Java JDK or JRE](http://www.oracle.com/technetwork/java/javase/downloads/index.html)의 버전이 7 이상이면 됩니다. `java -version` 명령어로 확인해 보세요.


>$ java -version
>java version "1.8.0_121"


- - -

### 설치

###### 패키지 매니저를 이용한 방법

[SDKMAN!](http://sdkman.io/)은 대부분의 유닉스 기반 시스템에서 멀티 소프트웨어들의 버전을 관리하는데 사용되는 툴입니다.

>$ sdk install gradle 4.6

[HomeBrew](https://brew.sh/)는 MacOS를 위한 패키지 매니저입니다.

>$ brew install gradle

[Scoop](http://scoop.sh/)는 Homebrew에 영감을 받아 탄생한 Windows용 커맨드라인 설치자입니다.

>$ scoop install gradle

[Chocolatey](https://chocolatey.org/)도 Windows 용 패키지 매니저입니다.

>$ choco install gradle

[MacPorts](https://www.macports.org/)는 MacOS에서 도구를 관리하기 위한 시스템입니다.

>$ sudo port install gradle

###### 수동 설치 방법

**Step 1. [Download](https://gradle.org/releases/)에서 최신 gradle 배포판 다운로드**

현재 Gradle의 릴리즈 버전은 2018년 2월 28일에 릴리즈된 4.6 버전입니다. 배포 ZIP 파일은 아래의 두 가지 형태로 제공됩니다.
* [Binary-only](https://gradle.org/next-steps/?version=4.6&format=bin)
* [Complete, with docs and sources](https://gradle.org/next-steps/?version=4.6&format=all)

만약 뭔가 이상하다면, Binary-only 버전을 선택하고 온라인에서 [docs](https://docs.gradle.org/current/userguide/userguide.html)와 [sources](https://github.com/gradle/gradle)을 확인하세요.

예전 버전으로 작업해야 한다구요? 그러면 [release page](https://gradle.org/releases/)를 참고하세요.

- - -

**Step 2. gradle 배포판 압축 해제**

**Linux와 MacOS 사용자**는 적당한 위치에서 배포판의 압축을 풀면 됩니다. e.g.:

>$ mkdir /opt/gradle
>$ unzip -d /opt/gradle gradle-4.6-bin.zip
>$ ls /opt/gradle/gradle-4.6
>LICENSE    NOTICE    bin    getting-started.html    init.d    lib    media  

**Microsoft Windows의 사용자**는 `C:\gradle` 디렉터리를 생성하고 그 안에 압축을 풀면 됩니다.

- - -

**Step 3. 시스템 환경 세팅**

**Linux와 MacOS 사용자** 는 gradle/bin의 경로를 환경 변수에 추가해야 합니다. e.g.:

>$ export PATH=$PATH:/opt/gradle/gradle-4.6/bin

**Microsoft Windows의 사용자**는 내 PC를 오른쪽 클릭해 속성 -> 고급 시스템 설정 -> 환경 변수로 들어갑니다.

그리고 `System Variabled`의 `path`에 `C:\Gradle\gradle-4.6\bin`을 입력하고 저장합니다.

- - -

**Step 4. 설치 확인**

console을 열고(Windows의 경우엔 command prompt) `gradle -v`를 입력합니다. 
그러면 gradle의 버전이 나타날 겁니다. e.g.:

>$ gradle -v
>
>------------------------------------------------------------
>Gradle 4.6
>------------------------------------------------------------

### Gradle Wrapper로 업그레이드

만약 Gradle 기반인 [Gradle Wrapper](https://docs.gradle.org/4.6/userguide/gradle_wrapper.html)를 사용하는 경우라면, `wrapper` task를 이용해 원하는 gradle 버전으로 쉽게 업그레이드할 수 있다.  

>$ ./gradlew wrapper --gradle-version=4.6 --distribution-type=bin

Gradle wrapper를 사용하는 경우에는 Gradle을 설치할 필요가 없습니다. `gradlew` 또는 `gradlew.bat`은 명시된 gradle의 버전을 알아서 다운로드하고 캐시합니다.

>$ ./gradlew tasks
>Downloading https://services.gradle.org/distributions/gradle-4.6-bin.zip
>...

- - -

### 다른 릴리즈들

다른 릴리즈와 각 checksums들은 [release page](https://gradle.org/releases/)에서 찾을 수 있을 겁니다.


### 마무리

gradle의 포스트 중 가장 간단한 installation을 번역하는데도 생각보다 꽤 긴 시간이 들었다.
대강 뜻은 알겠는데, 정리된 한글로 다시 쓰려니 되게 어려운 것 같다.  

다음은 [Gradle Tutorials and Guides](https://gradle.org/guides/#getting-started)의 포스트 중 [Creating Build Scans](https://guides.gradle.org/creating-build-scans)를 해야겠다.
포기 없이 gradle의 모든 post를 번역해보자! 

잘못된 점이나 모호한 설명이 있다면, 편하게 메일 주세요~ 적극적으로 반영하도록 하겠습니다!

- - -