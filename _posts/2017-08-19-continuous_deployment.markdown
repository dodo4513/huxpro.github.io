---
layout:     post
title:      "Jenkins X GitHub로 remote 서버에 자동 배포하기 + SSH"
subtitle:   "특정 repository가 푸시될 때, remote 서버에 자동 배포 하자!"
date:       2017-8-19
author:     "Xavy"
catalog:    true
tags:
    - Git
    - OAuth
    - Jenkins
---

### 시작하기 전에

 개발 환경에서는 여러 단계의 branch가 존재한다. 그 중 release branch는 real로 나가기 직전에 실제 DB의 환경으로 마지막 테스트를 하는 곳이다.(이는 표준이 아니고 우리 팀의 약속)
때문에 release branch에 푸시를 하는 상황은 곧 real에 수정사항이 나가기 직전 뿐이고 팀에서 한명이 대표로 푸시(머지)를 진행하기 때문에 배포로 인해 서버가 자꾸 껏다켜져서 다른 사람이 불편할 일도 없다.
(이에 반해 develop branch의 경우 다수의 개발자가 수시로 푸시를 하기떄문에 해당 branch를 바라보는 서버를 자동배포로 세팅한다면 서버가 수시로 restart 되기 떄문에, 다른 개발자나 QA 분들이 굉장히 불편하다) 
그래서 우리는 release branch는 푸시하면 바로 자동배포가 되도록 인프라를 구성하기로 했다.

다 하고 나니, 다시 세팅한다면 금방할 것 같은데, 모를땐 이것 저것 삽질을 하느라 꽤 오랜 시간이 걸렸다 ㅜㅜ
나중에 또 해야할 일이 생길지도 몰라 간략히 정리해 두고자 한다.

작업 환경 
- CentOS 6.8
- Jenkins 1.651.3
- GitHub plugin 1.26.1(jenkins plugin)
- Publish Over SSH plugin 1.17(jenkins plugin)
- Jenkins가 설치된 서버가 한대 있고, 웹 서비스가 구동 중인 서버가 따로 한대가 있는 환경임.

### 자동 배포하기!

###### 0 전체적인 자동배포 시나리오

1. 누군가 hook branch에 변경사항을 푸시한다.

2. GitHub가 푸시 이벤트를 감지하고 Jenkins에 WebHook을 보낸다.

3. Jenkins의 Job이 신호를 받는다.

4. Job은 해당 branch의 최신 소스를 받고 빌드해 war를 만든다.

5. 이어서 SSH 통신으로 remote 서버에 war 파일을 전송한다.

6. 전송 후 명령 스크립트로 기존 war 파일을 방금 빌드한 war로 교체한다.

7. 서버 환경에 따라 적절히 마무리 작업을 하면 끝!

###### 1 Jenkins 플러그인 설치

`Jenkins 관리 > 플러그인 관리` 에서 아래의 두 플러그인을 설치해야 한다. 이 둘을 설치하지 않을 경우 특정 메뉴가 보이지 않으니 꼭 설치해야 한다.
 
- GitHub plugin : Github의 특정 branch의 푸시 이벤트가 발생했을 때 알림을 받기 위해 필요.

- Publish Over SSH plugin : Jenkins가 빌드한 war 파일을 remote 서버로 보내주기 위해 필요.

###### 2 Jenkins job 이 특정 branch의 푸시 이벤트 받기

GitHub plugin은 jenkins가 GitHub의 hook을 받을 수 있도록 수동 모드와 자동모드를 제공한다. 
수동 모드는 사용자가 Git Repository setting로 가서 Hooks & services을 직접 등록해 사용하는 것이고, 
자동 모드는 사용자의 GitHub OAuth 토큰을 이용해 Jenkins가 자동으로 Hooks & services를 등록한다. 
우리는 `자동 모드를 사용`하겠다.

###### 2 - 1 GitHub OAuth 등록
 
 자동 모드는 Jenkins에 사용자의 GitHub OAuth 토큰을 먼저 등록해야 한다.
 `Jenkins 관리 > 시스템 설정`에서 GitHub 세팅을 찾는다.
 
<img class="shadow" src="/img/my-post/20170819_continuous_deployment/1_github.jpg" alt="github setting">
 
API URL : 자신이 사용하는 GitHub 주소에 /api/v3를 붙이면 된다.

그리고 Credentials옆의 Add를 눌러 자신의 GitHub Personal access 토큰을 입력한다.
이 토큰은 `GitHub 계정의 Setting > Personal access tokens`에서 생성하면 된다. 이 때 토큰은 `admin:repo_hook`의 권한이 있어야 한다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/2_add.jpg" alt="add">

kind는 Secret text이고 아래의 Secret에 토큰값을 ID와 Description에는 적당히 입력한다.

- - -

###### 2 - 2 Jenkins Job 생성

다음은 푸시 이벤트를 받을 Freestyle project Job을 만든다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/3_job.jpg" alt="job">

소스코드 관리에서 Git을 선택하고 URL과 계정, 그리고 자동배포할 branch를 입력한다. 
아래와 같이 세팅하면 hook 브랜치에 누군가 푸시할 때 해당 Job이 동작하게 된다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/4_job2.jpg" alt="job2">

마지막으로 빌드 유발에서 `GitHub hook trigger for GITScm polling` 항목을 선택한다.
인터넷의 자료를 뒤지다 보면 Build when a change is pushed to GitHub 항목을 선택하라고 안내하는 곳이 많은데, 최신 버전에서는 GitHub hook trigger for GITScm polling로 명칭이 변경되었다.
명칭 뿐 아니라 사용법도 달라진 것 같다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/5_job3.jpg" alt="job3">

여기까지 세팅한 후 Job을 저장하고, Git repository setting의 Hooks & services에 들어가면 Jenkins가 자동으로 등록한 Webhooks를 볼 수 있다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/6_webhooks.jpg" alt="webhooks">

위에서 주소를 보면 내 Jenkins 주소 뒤에 /github-webhook/이 붙어있는 것을 볼 수 있다. 만약 GitHub plugin의 자동모드가 아닌 수동모드를 사용하고자 한다면 위와 같이 서버URL/github-webhook/ 형식으로 Webhooks을 직접 등록해주면 된다. 

이제 Jenkins Job에는 GitHub Hook Log라는 메뉴가 있을 것이고 branch에 푸시하면 hook이벤트를 수신한 것을 직접 볼 수 있다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/7_hook.jpg" alt="hook">

###### 3 빌드

이제 아까 작업한 Job에 빌드도 추가하자(아래는 gradle 프로젝트 예시)

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/8_build.jpg" alt="build">

Jenkins는 빌드 후 war을 `JENKINS_HOME/jobs/$Job_name/workspace`에 저장한다. 
우리는 이 파일을 Publish Over SSH plugin을 이용해 remote 서버로 전송할 것이다. 
해당 플러그인은 ssh 인증을 미리 받아두고 빌드 전후로 명령 혹은 파일을 remote 서버로 전송하는 기능을 가지고 있다. 

###### 4 remote 서버로 SSH 통신을 이용해 war 전송하기 

먼저 jenkins에 remote 서버의 ssh 인증을 등록하자. GitHub OAuth token을 입력했던 것 처럼, `젠킨스 메인 > Jenkins 관리 > 시스템 설정`에서 Publish over SSH으로 간다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/9_ssh_server.jpg" alt="ssh_server">

- Passphrase : 위 스샷에는 뭐가 입력된 것 처럼 보이지만, 자동 로그인을 해야하므로 아무것도 넣지 않은 상태다.
- Path to key : Jenkins 서버의 id_rsa 파일이 있는 위치.
- Key : Path to key에 id_rsa 파일의 위치를 넣어도 되고 Key에 파일의 내용을 가져와 붙여넣어도 된다. 둘다 입력 시 우선순위는 Path to key가 더 높다고 한다.
- Name : 적당히 서버의 이름을 입력하면 됨
- Hostname : remote 서버의 IP
- Username : `remote 서버의 계정명`을 입력해야 한다!
- Remote Directory : 배포할 서버의 기본 workspace라 보면 된다.(파일 전송 시 디렉토리를 정해주지 않는다면 여기로 감.)

**Remote 서버의 authorized_keys에 Jenkins 서버의 id_rsa.pub의 공개키를 넣어두면 된다. 참고로 CentOS 기준으로 .ssh파일의 권한은 700 authorized_keys는 600 이 아니면 ssh 인증이 동작하지 않는다.(remote 서버에는 .ssh가 없어서 직접 만들어줬는데 권한 설정을 안해줘서 ssh가 동작을 안해서... 알기까지 고생했다.)**

Remote 서버의 ssh 인증을 등록했으니 Jenkins Job으로 다시 돌아가서 빌드 후 조치 > Send build artifacts over SSH를 등록한다.

<img class="shadow" src="/img/my-post/20170819_continuous_deployment/10_ssh.jpg" alt="ssh">

아까 등록한 SSH Server를 선택한다.

- Source files : 아까 Jenkins는 빌드 후 JENKINS_HOME/jobs/$Job_name/workspace에 저장한다고 했는데, 해당 디렉토리 기준으로 전송할 파일의 위치를 작성한다.
- Remove prefix : 위 예제처럼 디렉토리 안에 파일이 있는 경우 디렉토리까지 다 전송되게 되는데 이곳에 디렉토리를 명시하면 해당 디렉토리는 전송되지 않고 안의 파일만 전송된다.
- Remote directory : 아까 설정한 remote server의 workspace 기준으로 어디다 전송할 것인지. 
- Exec command : 파일을 전송한 후 실행할 리눅스 명령어. remote 서버에 배포관련 script를 미리 가따놓고 여기서 실행하면 된다.

자신의 서버 환경에 맞게 exec command에 적당히 배포 명령을 넣어두면 되겠지?

잘 입력했는데도 위 스샷처럼 Either Source files, Exec command or both must be supplied라며 에러가 나는 경우도 있는데 이는 특정 버전에서 나는 버그라고 한다. 무시하면 될 것 같다.

### 마치며

생각보다 최신 자료도 많이 없고, 회사에서 세팅을 하다보니 정책적으로 안되는 건지, 내가 세팅을 잘못한 건지 아리송한 경우가 많아 힘들었다.
개발하며 잘 안된다고 Jenkins 욕을 많이 했는데, 다 하고 나니 정말 잘 만든 프로젝트인 것 같다 ㅎㅎ(안되는건 모두 내 잘못..) 

잘못된 점이나 애매한 설명이 있다면, 편하게 메일 주세요~ 수정하도록 하겠습니다.

### 참고

[Jenkins GitHub Plugin](https://wiki.jenkins.io/display/JENKINS/Github+Plugin)
[Jenkins Publish Over SSH Plgin](https://wiki.jenkins.io/display/JENKINS/Publish+Over+SSH+Plugin)