---
layout:     post
title:      "Facebook Oauth 직접 구현하기"
subtitle:   "Facebook Oauth login flow를 직접 구현해보자."
date:       2017-2-12
author:     "Xavy"
catalog:    true
comments: true
tags:
    - Oauth
    - Spring
---

### Facebook Oauth login flow 직접 빌드하기
Facebook 로그인을 구현하는 가장 쉽고 편한 방법은 Facebook이 제공하는 공식 SDK를 사용하는 것이지만, Oauth 동작을 살펴보기 위해 이들을 사용하지 않고 직접 빌드해봤다.

`Spring 4.3.5 RELEASE + MAVEN` 웹 프로젝트 환경에서 할 것이며, 혹 본 포스트의 내용에 잘못된 점이나 개선할 점이 있다면 알려주세요!

###### 전체적인 흐름

전체적인 Oauth 인증 흐름은 아래와 같다.
<img class="shadow" src="/img/my-post/2/oauth_flow.png" >
이미지 출처:[Linked in OAuth token processing using DataPower® IDG - Part 1: OAuth 2.0 basics & DataPower® OAuth Support](https://www.linkedin.com/pulse/oauth-token-processing-using-datapower-idg-part-1-20-julian-smiles)

- - -

###### Facebook App만들기

[Facebook for developers - app 만들기](https://developers.facebook.com/apps/)

위 url을 따라가보면 App을 만들 수 있다. 적당히 만들면 아래와 같은 화면을 볼 수 있다. 이 때 `앱 ID와 앱 시크릿 코드`는 나중에 사용해야하니 잘 챙겨 두면된다..
<img class="shadow" src="/img/my-post/2/app_id_pw.png" >

- - -

###### Facebook 버튼

```html
<a href="facebookSignin.do">
	<button type="button" class="btn btn-primary">Facebook 로그인</button>
</a>
```

아주 단순한 Facebook 로그인 버튼에서 시작한다. 위 버튼을 누르면 아래에서 볼 수 있는 `getfacebookSigninCode(HttpSession session)`으로 간다.

- - - 

###### Facebook 인증 페이지로 리다이렉트

아까 봤던 Oauth 인증 흐름 이미지에서 `3.Login via Authorization Server`에 해당한다.

```java
@RequestMapping(value = "/facebookSignin.do")
public String getfacebookSigninCode(HttpSession session) {
	logger.debug("facebookSignin");

	String facebookUrl = "https://www.facebook.com/v2.8/dialog/oauth?"+
					"client_id="+FACEBOOK_CLIENT_ID+
					"&redirect_uri=http://localhost:8080/letsparty/facebookAccessToken.do"+
					"&scope=public_profile,email";

	return "redirect:"+facebookUrl;
}
```

Facebook의 로그인 대화상자 호출 및 리디렉션 URL을 설정한다.

아까 봤던 Facebook 버튼이 클릭되면 위 Controller의 Method가 실행된다.

- client_id : 아까 만든 Facebook의 app id.
- redirect_uri : Facebook 로그인 후 돌아올 uri.
- scope : [Facebook에서 가져올 데이터 권한 목록.](https://developers.facebook.com/docs/facebook-login/permissions)
- state : 매개변수를 생성한 다음 로그인 요청에 사용하여 CSRF 보호용.

위 Url로 redirect되면 Facebook 로그인창이 나타나며, 사용자가 로그인 시 위에 작성한 `redirect_url`로 돌아온다.

- - -

###### Authorization code

사용자가 Facebook 로그인 성공시 `redirect_url`로 돌아오는데 Authorization Code도 함께 돌아온다.
이는 Oauth 인증 흐름 이미지에서 `4.Redirect to Client URI with Authorization Code`에 해당한다.

```java
@RequestMapping(value = "/facebookAccessToken.do")
public String getFacebookSignIn(String code, HttpSession session, String state) throws Exception {
	logger.debug("facebookAccessToken / code : "+code);
	
	String accessToken = requesFaceBooktAccesToken(session, code);
	facebookUserDataLoadAndSave(accessToken, session);
	
	return "redirect:main.do";
}
```

`Authorization code`는 위 메서드의 code 파라미터에 들어오고, 이를 이용해 AccessToekn을 다시 얻어와야 한다. 위 코드에서 `requesFaceBooktAccesToken(session, code)`가 그 역할을 한다.
위 Oauth 인증 흐름 이미지에서는 `6.Send Auth Code with Client id & secret`이다.

```java
private String requesFaceBooktAccesToken(HttpSession session, String code) throws Exception {
	String facebookUrl = "https://graph.facebook.com/v2.8/oauth/access_token?"+
					 	"client_id="+FACEBOOK_CLIENT_ID+
					 	"&redirect_uri="+REDIRECT_URL+
					 	"&client_secret="+FACEBOOK_CLIENT_SECRET_KEY+
					 	"&code="+code;
	
	HttpClient client = HttpClientBuilder.create().build();
	HttpGet getRequest = new HttpGet(facebookUrl);
	String rawJsonString = client.execute(getRequest, new BasicResponseHandler());
	logger.debug("facebookAccessToken / raw json : "+rawJsonString);
	
	JSONParser jsonParser = new JSONParser();
	JSONObject jsonObject = (JSONObject) jsonParser.parse(rawJsonString);
	String faceBookAccessToken = (String) jsonObject.get("access_token");
	logger.debug("facebookAccessToken / accessToken : "+faceBookAccessToken);
	
	session.setAttribute("faceBookAccessToken", faceBookAccessToken);
	
	return faceBookAccessToken;
}
```

AccessToken을 얻기위해 HttpClient로 다시 한번 통신을 한다.

- client_id : 아까 만든 Facebook의 app id.
- redirect_uri : Facebook 로그인 후 돌아올 uri.
- client_secret : 이것도 아까만든 app의 secret id.
- code : 아까 받은 `Authorization Code.`

위 예제에서는 사용하지 않았지만 아까 code 요청 url에서 state를 보냈다면, 위 메서드에서 code 뿐 아니라 state도 받을 수 있다.

 **`Authorization code`를 요청할 때와 이를 이용해 `Access Token`을 요청할 때 같은 redirect_uri를 사용해야 한다.**

- - -

###### Access Token을 가지고 Facebook 정보 요청하기

위 `getFacebookSignIn(String code, HttpSession session, String state)` 메소드 안에 있는 `facebookUserDataLoadAndSave(accessToken, session)`가 정보 요청을 한다.

```java
private void facebookUserDataLoadAndSave(String accessToken, HttpSession session) throws Exception {
    String facebookUrl = "https://graph.facebook.com/me?"+
            "access_token="+accessToken+
            "&fields=id,name,email,picture";

    HttpClient client = HttpClientBuilder.create().build();
    HttpGet getRequest = new HttpGet(facebookUrl);
    String rawJsonString = client.execute(getRequest, new BasicResponseHandler());
    logger.debug("facebookAccessToken / rawJson!  : "+rawJsonString);

    JSONParser jsonParser = new JSONParser();
    JSONObject jsonObject = (JSONObject) jsonParser.parse(rawJsonString);
    logger.debug("facebookUserDataLoadAndSave / raw json : "+jsonObject);

	/* 가져온 데이터를 서비스에 맞춰 가공하는 로직*/

}
```

처음 Facebook 인증 페이지로 리다이렉트할 때 함께 보냈던`scope`의 권한에 맞춰 위와같이 사용자의 facebook 정보를 요청할 수 있다. 아까전에 나는 `public_profile`,`email`를 요청했었고, 이에 맞춰 위에 소스에서 `fields=id,name,email,picture`로 요청 했다.

Json 형태로 정보가 넘어오고 이를 각자의 서비스에 맞게 이용하면 될 것 같다.

### 마치며

Oauth 인증 흐름 이미지가 큰 도움이 되었다. 순서대로 하나하나 따라가며 Facebook 레퍼런스를 참고해 구현하니 해볼만 했다. 레퍼런스가 정말 잘 되있더라. 그러나 기능은 동작하지만 위 방식대로 구현하는 것이 실제로 올바른 방법인지는 좀더 고민해 볼 필요가 있을 것 같다.

### 추가한 Maven 라이브러리

[HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient/4.5.2)

[JSONParser, JSONObject](https://mvnrepository.com/artifact/com.googlecode.json-simple/json-simple/1.1.1) 

### 참고

[Facebook 로그인 플로 직접 빌드](https://developers.facebook.com/docs/facebook-login/manually-build-a-login-flow)

[Facebook 그래프 API 탐색기](https://developers.facebook.com/tools/explorer/)