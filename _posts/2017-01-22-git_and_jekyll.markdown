---
layout:     post
title:      "심플하고 블로그 지향적인 정적 사이트 - Github & Jekyll"
subtitle:   "github와 jekyll을 이용해 블로그를 작성하자!"
date:       2017-1-22
author:     "Xavy"
catalog:    true
tags:
    - GIT
---

### Jekyll

###### Jekyll&Github
 **Jekyll**은 아주 심플하고 블로그 지향적인 적정 사이트 생성기이다. 단순하게 텍스트를 작성하면 Liquid 렌더러를 통해 가공해서 정적 웹 사이트를 만들어 준다. 기본적인 틀은 다 만들어져 있기 때문에 위 과정이 정말 간단하다.  게다가 **Github에서 무료로 호스팅**을 할 수있기 때문에 더 매력적인 것 같다.
 
###### 테마 선정하기
 Jekyll repository에서 받아서 커스텀해도 되지만 나같이 귀찮은 사람들을 위해 만들어둔 테마가 많이 있다.
 나중에 테마를 변경하는 것은 어렵기 때문에 잘 골라야 한다.
 
[Jekyll Wiki에 있는 테마 목록들](https://github.com/jekyll/jekyll/wiki/Themes)

[현재 블로그에 사용된 테마 - Huxpro](https://github.com/Huxpro/huxpro.github.io)

###### 블로그 만들기(Github 호스팅)
<img class="shadow" src="/img/my-post/1/jekyll_1.png" alt="jekyll1">
 1. 원하는 테마를 골랐다면 해당 Git repository에서 fork를 한다.
<img class="shadow" src="/img/my-post/1/jekyll_2.png" alt="jekyll2" >
 2. 내 repository로 가져온 후 setting에 들어가서 repository name을 정해준다. 이 이름이 나중에 url이 된다. `이 때 https://github_id.github.io/ 형식으로 이름을 정해야 해당 주소로 바로 접속이 가능하다. 그렇지 않으면 https://github_ID.github.io/your_repository_name/이 된다.`
<img class="shadow" src="/img/my-post/1/jekyll_3.png" alt="jekyll3">
 3. 다시 한번 Setting의 Github pages 항목에서 Source를 적당한 branch로 선택 후 저장한다.

- 3번 그림에서 나오는 CNAME 관련 에러는 자신의 Repository에서 해당 파일을 지우면 된다.

###### 관리 하기

 - 기본적인 세팅은 _config.yml에서 하면 된다.
 - 포스트를 작성하고 올릴때는 _posts 폴더 안에 마크다운 문법으로 글을 작성하고 Github에 푸시하면 호스트에 반영된다.

### 마치며
 예전부터 내 생각이나 지식을 블로그에 차곡차곡 정리하면 어떨까 생각하며 살았다. 그러나 실제로 블로그를 한다는건 생각보다 손이 많이 가는 일이었고 조금 열심히 하다가 제풀에 지쳐 나가떨어졌다. 이번에 알게된 jekyll이라면 아주 가끔이라도 꾸준히 글을 쓸 수 있지 않을까? 기대한다.

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 본문 내 삽입 광고1 -->
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="ca-pub-6655397595619261"
     data-ad-slot="9573751638"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

### 참고
[한글로 번역된 jekyll 가이드](https://jekyllrb-ko.github.io/)

[Github의 jekyll repository ](https://github.com/jekyll/jekyll)
