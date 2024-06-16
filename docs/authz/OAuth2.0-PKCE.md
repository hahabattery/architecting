---
layout: default
title: OAuth2.0 PCKE
parent: Authentication And Authorization
nav_order: 3
---

[OAuth 2.0 Playground](https://www.oauth.com/playground/) 에서 OAuth 2.0 동작을 테스트해볼 수 있다.

```
POST https://authorization-server.com/token

grant_type=authorization_code
&client_id=CKw2bkLjI-6Bs3wwgl7OBUgz
&client_secret=F3n7fXMtVwGJ5lXqTmwUHoNUp6O0qN1YYjkRkrQ7ZD6Kbnvt
&redirect_uri=https://www.oauth.com/playground/authorization-code-with-pkce.html
&code=Uyz9EU-QeRfW4Kt-nUnq4s7NxMuFjJLhT3DVHD6VyLn8Mc5Q
&code_verifier=EAp91aanXdoMcoOc2Il55H3UDDIV909k9olEEcl6L24J6_9X
```

# PKCE ( Proof Key for Code Exchange )

발음 ""픽시"

PKCE는 OAuth2의 Authorization Code Grant flow에서 좀 더 강화된 보안을 제공해주는 Authorization Code Grant flow의 확장 버전이다(보안이 낮은 수준인 브라우저인 경우를 보완하기 위해서 나왔다).

Authorization Code Injection Attack을 막을 수 있다.




 * Authorization Code Grant Flow
![](/images/authz/Authorization-Code-Flow-Diagram.png)

위 그림은 Authorization Code Flow를 나타내는 그림.

### 용어 정리
 * Client: Resource를 요청하는 주체입니다. 예를들어, 디바이스나 WAS 등이다.
 * Resource Owner: Client가 요청하고자 하는 리소스의 소유자입니다. 예를들어, Client 서비스를 이용하는 사용자가 있다.
 * User-Agent: Authorizaiton Code Grant Flow는 http redirection 베이스로 Authorization을 진행하기 때문에 Resoure-Owner의 User-Agent와 상호작용합니다. 예를 들어, 웹 브라우저가 있다.
 * Authorization Server: OAuth2를 제공하는 서버.

 * 이 예에서는 인증서버가 authorization-server.com 이고, 사용자가 이용하는 사이트가 www.oauth.com 이다.

## A. Client가 Resource Owner의 Authorization를 요청하기 위해 Authorization Server로 redirect를 시킵니다.

 * User-Agent -> Authorization Server 요청 (리다이렉트된...)
```
https://authorization-server.com/authorize?
  response_type=code
  &client_id=CKw2bkLjI-6Bs3wwgl7OBUgz   <= 이 아이디는 Client 사이트가 인증서버를 제공하는 회사(구글,페이스북)에서 사전에 받아야 한다.
  &redirect_uri=https://www.oauth.com/playground/authorization-code-with-pkce.html
  &scope=photo+offline_access     <= 이 예에서는 사진도 요청한다!
  &state=w7pneFNa8aF2i5f_         <= 인증서버는 이 정보를 Client사이트로 리다이렉트할 때 그대로 넣어준다.
                                  <= CSRF 공격을 방어하기 위해서 사용될 수 있다.
```

아마도 사용자는 이 단계에서 아래와 같은 화면에서 구글 아이콘을 클릭하게 될 것이다.
![](/images/authz/SNS-Login-Screen-1.png)

이 단계에서 만약 자동로그인이나 세션이 유효하지 않은 상태로 로그인이 필요하면, 카카오나 페이스북에 로그인하게 됨.


## B. 유저가 인증을 합니다.
인증 서버의 인증 방식(예를 들어서 아이디/비번 형태)을 통해서 인증

## C. 인증서버에서 인증이 성공하면 Client 사이트(사용자가 이용하려던 사이트)의 Redirect Url로 Authorization Code와 state를 전달합니다.

 * A단계에서 전달했던 redirect_url로 Code와 State가 전달됩니다.
```
https://www.oauth.com/playground/authorization-code-with-pkce.html?
	state=w7pneFNa8aF2i5f_
    &code=Uyz9EU-QeRfW4Kt-nUnq4s7NxMuFjJLhT3DVHD6VyLn8Mc5Q
```



## D ~ E. Client는 전달받은 Authorization Code로 Access Token을 요청합니다.

```
POST https://authorization-server.com/token

grant_type=authorization_code
&client_id=CKw2bkLjI-6Bs3wwgl7OBUgz
&client_secret=F3n7fXMtVwGJ5lXqTmwUHoNUp6O0qN1YYjkRkrQ7ZD6Kbnvt
&redirect_uri=https://www.oauth.com/playground/authorization-code-with-pkce.html
&code=Uyz9EU-QeRfW4Kt-nUnq4s7NxMuFjJLhT3DVHD6VyLn8Mc5Q    <= authorization code
```

Authorization Code는 인증서버에서 사용자가 인증받고 응답으로 얻은 코드이며, 이 코드를 Client 사이트(사용자가 이용하는 서비스를 제공하는 사이트)에서 인증서버(구글,페이스북)으로 다시 보낸다.
이 응답으로 Client 사이트는 Access Token과 Refresh Token을 얻게 된다.


 * 인증서버에서 받은 응답의 예.
```json
{
  "token_type": "Bearer",
  "expires_in": 86400,
  "access_token": "ytAYS7TW_7qEhdKBOO1B_GhN67fxIRsW7TGcl7_nF6xfPq_as2TDWgNYKOg4ZQfGGAsHOvfi",
  "scope": "photo offline_access",
  "refresh_token": "bfxELvumTFsXScFiHbNJ4KOf"
}
```

그리고, 그 다음에 Client 사이트는 인증서버(구글,페이스북)에 발급받은 Access Token을 이용해서 사진,전화번호같은 사용자데이터를 요청한다.
(예를 들어서 구글드라이브의 draw.io라고 하면, access token으로 사용자의 파일에 접근하게 된다.)



# 그래서 PKCE란?

PKCE는 위에서 정리한 flow에 Code Verifier와 Code Challenge를 추가하여 Authorization Code Grant Flow에서 Authrozization Code가 탈취당했을 때 Access Token을 발급하지 못하도록 막아줄 수 있습니다.

Code Verifier와 Code Challenge는 Client가 생성합니다.

Code Verifier 생성 규칙

여기서 생성한 Code Verifier는 인증서버로 보내지는 않고, 나중 사용을 위해서 저장되어야 한다. (쿠키나 로컬스토리지?)

>48 ~ 128 글자수를 가진 Random String.
>A-Z a-z 0-9 -._~ 문자들로만 구성됨
>
>Code Challenge 생성 규칙
>
>선택한 Hash 알고리즘으로 Code Verifier를 Hashing 한 후 Base64 인코딩을 한 값
>
>ex) Base64Encode(Sha256(Code Verifier))



### 1. 위의 A 단계에서 Code Challenge와 hash 함수 종류를 쿼리 파라미터에 추가
```
https://authorization-server.com/authorize?
  response_type=code
  &client_id=CKw2bkLjI-6Bs3wwgl7OBUgz
  &redirect_uri=https://www.oauth.com/playground/authorization-code-with-pkce.html
  &scope=photo+offline_access
  &state=w7pneFNa8aF2i5f_
  &code_challenge=HVoKJYs8JruAxs7hKcG4oLpJXCP-z1jJQtXpQte6GyA  <= code verfier는 보내지 않고, Hashing하고 Base64로 인코딩한 값만 보낸다.
  &code_challenge_method=S256
```

A요청에 사용자가 로그인되어있는 상태가 아니면, 로그인 페이지로 리다이렉트하고(B),
Client사이트에 이용을 최초로 요청하는 경우라면, 권한을 부여하는 페이지(C)가 다시 표시된다.


### 2. 위의 D ~ E 단계에서는 Client사이트는 인증서버에게 Code Verifier를 전달

```
POST https://authorization-server.com/token

grant_type=authorization_code
&client_id=CKw2bkLjI-6Bs3wwgl7OBUgz
&client_secret=F3n7fXMtVwGJ5lXqTmwUHoNUp6O0qN1YYjkRkrQ7ZD6Kbnvt
&redirect_uri=https://www.oauth.com/playground/authorization-code-with-pkce.html
&code=Uyz9EU-QeRfW4Kt-nUnq4s7NxMuFjJLhT3DVHD6VyLn8Mc5Q
&code_verifier=EAp91aanXdoMcoOc2Il55H3UDDIV909k9olEEcl6L24J6_9X
```


### 3. Authorization Server가 A단계에서 전달했던 code_challenge와 D ~ E 단계에서 전달한 code_verifier를 해싱하고 base64 인코딩하여 비교.


### 4. 검증이 되면 Access Token 발급


### 결론 PCKE는 Authorization Code가 중간자에 의해 탈취되어 사용되는 것을 막기 위한 방법이다.

사용자의 PC나 XSS와 같은 방식으로 code verfier가 탈취된다면, 중간자 공격을 막는 노력도 무위에 돌아가게 된다.


# 관련 리소스

https://tools.ietf.org/html/rfc6749#section-4.1

https://oauth.net/2/pkce/

https://www.oauth.com/playground/authorization-code-with-pkce.html
