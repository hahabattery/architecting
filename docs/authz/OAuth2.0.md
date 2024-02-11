---
layout: default
title: OAuth 2.0
parent: Authentication And Authorization
nav_order: 3
---

OAuth (Open Authorization)

OAuth 2.0는 다른 application에서 사용자의 데이터에 접근하는 것을 허가를 해준다는 의미이다.

OAuth를 authorization이나 delegated authorization이라고 하기도 한다.

> In many ways, you can think of the OAuth token as a "access card" at any office/hotel. These tokens provides limited access to someone, without handing over full control in the form of the master key.


페이스북/구글등의 로그인 인증을 이용하는 경우, 대부분은 Resource Server(페이스북 자체 API)를 사용하지 않는다다.

따라서 Access Token, Refresh Token은 실제로 잘 쓰이지 않을 것임. 

또한 Client에서 access token을 검증할 수도 없기 때문에, 페이스북/구글등의 로그인 인증시에 받은 access token은 사용되지 않는다.

따라서 OAuth2.0 인증이 완료되면, 세션/쿠키(http-based session)나 토큰기반 인증 방식으로 client 나름의 인증 처리를 하게 되고,

Authorization Server로 부터 얻는 고유 id값을 저장해서 DB에서 소셜 로그인용 회원관리를 진행하게된다.

# 리소스
 * [Microservices security with Oauth2](https://github.com/piomin/sample-spring-oauth2-microservices)


# 용어

![](doc/imgs/OAuth2-Terminology.png)

https://oauth.net/2 <= OAuth 에 대한 용어와 이론, grant type에 대해서 나와있음


# OAuth grant type
The OAuth framework specifies several grant types for different use cases.

 * Authorization Code Grant Type
 * PCKE
 * Client Credentials
 * Device Code
 * Refresh Token
 * Implicit Flow(Legacy) <= Deprecated
 * Resource Owner Credentials(Password) Grant(Legacy) <= Deprecated


### Authorization Code grant type

| ![](doc/imgs/OAuthFlow-AuthorizationCodeGrantType.png) |
|:--:|
| *Authorization Code Grant Type* |

step 2와 3에서 client가 Auth Server 로 보내는 정보
 * client_id
   + the id which identifies the client appliction by the Auth Server. This will be granted when the client register first time with the Auth server.
 * redirect_uri
   + the URI value which the Auth server needs to redirect post successful authentication. If a default value is provided during the registration then this value is optional.
 * scope
   + similar to authorities. Specifies level of access that client is requesting like "READ"
 * state
   + CSRF token value to protect from CSRF attacks
 * response_type
   + With the value 'code' which indicates that we want to follow authorization code grant


step 5 에서 client가 Auth Server로 보내는 정보
 * code
   + the authorization code received from the above steps
 * clinet_id, client_secret
   + the client credentials which are registered with the auth server. Please note that these are not user credentials
 * grant_type
   + With the value 'authorization_code' which identifies the kind of grant type is used.
 * redirect_uri




### Implicit grant type
**주의)** Implicit grant type 대신에 Authorization Code grant type을 사용할 것

> If the user approves the request, the Auth server will redirect the redirect the browser back to the redirect_uri specified by the application, adding a token and state to the fragment part of the URL.

아래는 이해를 위해서 정리한 내용.

Authorization Code Grant type의 경우 Auth Server에 2번 요청을 보내서 authorization code와 access token을 얻는다.
 * 1. authorization server는 유저가 credentials(사용자명/비번)을 가지고 인증 서버와 직접 상호 작용했는지 확인.
 * 2. client는 Auth server에서 발금한 client id와  authorization code를 가지고 access token을 발행한다.

위의 1,2번 스텝을 한번에 처리하는 방식을 implicit grant type 이라고 한다.

| ![](doc/imgs/OAuthFlow-ImplicitGrantType.png) |
|:--:|
| *Implicit Grant Type* |


step 3에서 client가 Auth Server로 보내는 정보
 * client_id
   + the id which identifies the client appliction by the Auth Server. This will be granted when the client register first time with the Auth server.
 * redirect_uri
   + the URI value which the Auth server needs to redirect post successful authentication. If a default value is provided during the registration then this value is optional.
 * scope
   + similar to authorities. Specifies level of access that client is requesting like "READ"
 * state
   + CSRF token value to protect from CSRF attacks
 * response_type
   + With the value 'token' which indicates that we want to follow implicit grant type


### Resource Owner Credentials(Password) grant type
**주의)** Not Recommened for production

> We use this authentication flow only if the client, Auth server and resource servers are maintained by the same organization.
>
> This flow will be usually followed by the enterprise applications who want to separate the Auth flow and business flow.
> Once the Auth flow is seprarated different applications in the same organization can leverage it.

| ![](doc/imgs/OAuthFlow-PasswordGrantResourceOwnerCredentialsGrantType.png) |
|:--:|
| *Resource Owner Credentials(Password) grant type* |

step 2에서 client가 Auth Server로 보내는 정보
 * client_id, client_secret
   + the credentials of the client to authenticate itself.
 * scope
   + similar to authorities. Specifies level of access that client is requesting like "READ"
 * username, password
   + Credentials provided by the user in the login flow
 * grant_type
   + With the value 'password' which indicates that we want to follow password grant type


### Client Credentials grant type

> This is the most simplest grant type flow in OAuth2.
>
> We use this authentication flow only if there is no user and UI involved(사용자의 인터렉션이 없이 client_id와 client_secret이 맞으면 토큰을 발급해줌). Like in the scenarios where 2 different application want to share data between them using backend APIs.

| ![](doc/imgs/OAuthFlow-ClientCredentialsGrantType.png) |
|:--:|
| *Client Credentials grant type* |


step 1에서 client가 Auth Server로 보내는 정보
 * client_id, client_secret
   + the credentials of the client to authenticate itself
 * scope
   + similar to authorities. Sepecifies level of access that client is requesting like "READ"
 * grant_type
   + With the value 'client_credentials' which indicates that we want to follow client credentials grant type



### Refresh Token grant type

>This flow will be used in the scenarios where the access token of the user is expired. Instead of asking the user to login again and again, we can use the refresh which originally provided by the Authz server to reauthenticate the user.
>
>Though we can make our acess tokens to never expire but it is not recommended considering scenarios where the tokens can be stole if we always use the same token
>
>Even in the resource owner credentials grant types **we should not store the user credentials for reauthentication purpose instead we should reply on the refresh tokens**


| ![](doc/imgs/OAuthFlow-RefreshTokenGrantType.png) |
|:--:|
| *Refresh Token grant type* |

step 3에서 client가 Auth Server로 보내는 정보
 * client_id, client_secret
   + the credentials of the client to authenticate itself
 * refresh_token
   + the value of the refresh token received initially
 * scope
   + similar to authorities. Sepecifies level of access that client is requesting like "READ"
 * grant_type
   + With the value 'refresh_token' which indicates that we want to follow client credentials grant type


### PKCE(Proof Key for Code Exchange)
>When public clients(eg., native and single-page application) request Access Token, some additional security concnerns are posed that are not mitigated by the Authorization Code Flow alone.
>This is because public clients cannot securely store a Client Secret.
>
>Given these situations, OAuth 2.0 provides a version of the Authorization Code Flow for public client applications which makes use of a Proof Key for Code Exchange (PKCE).


**PCKE-enhanced Authorization Code Flow**
* 1. Once user clicks login, client app creates a cryptographically-random **code_verifier** and from this generates a **code_challenge**.
* 2. code challenge is a Base64-URL-encoded string of the SHA256 hash of the code verfier.
* 3. Redirects the user to the Authorization Server along with the code_challenge.
* 4. Authorization Server stores the code_challenge and redirects the user back to the application with an authorization code, which is good for one use.
* 5. Client App sends the authorization code and the code_verifier(created in step 1) to the Authorization Server.
* 6. Authorization Server verifies the code_challenge and code_verifier. If they are valid it respond with ID Token and Access Token (and optionally, a Refresh Token).


| ![](doc/imgs/OAuthFlow-PKCEGrantType.png) |
|:--:|
| *PKCE grant type* |

step 3 client가 Auth Server로 보내는 정보
 * client_id
   + the id which identifies the client appliction by the Auth Server. This will be granted when the client register first time with the Auth server.
 * redirect_uri
   + the URI value which the Auth server needs to redirect post successful authentication. If a default value is provided during the registration then this value is optional.
 * scope
   + similar to authorities. Specifies level of access that client is requesting like "READ"
 * state
   + CSRF token value to protect from CSRF attacks
 * response_type
   + With the value 'code' which indicates that we want to follow authorization code grant
 * code_challenge
   + XXXXXXXXX - The code challenge generated as previously described
 * code_challenge_method
   + S256 (either plain or S256)

step 5 client가 Auth Server로 보내는 정보
 * code
   + the authorization code received from the above steps
 * client_id, client_secret (optional)
   + the client credentials which are registered with the auth server. Please note that these are not user credentials
 * grant_type
   + With the value 'authorization_code' which identifies the kind of grant type is used
 * redirect_uri
 * code_verifier
   + The code verifier for the PKCE request, that the app originally generated before the authorization request.


# 리소스서버에서 토큰을 validation하는 방법

### Direct API Call
![](./doc/imgs/ResourceServerTokenValidation-DirectAPICall.png)

매번 인증서버쪽으로 확인, 부하가 생김

### Common DB
![](./doc/imgs/ResourceServerTokenValidation-CommonDB.png)

인증서버에서 Access Token 발급시에 DB에 저장하고, 리소스 서버에서 DB에서 조회해서 발급한 Access Token이 유효하다고 판단하는 방식

### Using Certificates
![](./doc/imgs/ResourceServerTokenValidation-UsingCertificates.png)

이게 가장 권장됨




---
# OAuth와 JWT의 차이

> The main difference between the OAuth and JWT implementations is that OAuth uses an external provider while the JWT implementation generates its own JWT tokens.






# Github

 * github 매뉴얼
   + https://docs.github.com/en/developers/apps/building-oauth-apps/authorizing-oauth-apps
 * 함께 보면 좋은 내용
   + https://spring.io/guides/tutorials/spring-boot-oauth2/#github-register-application


### Github이 지원하는 OAuth 2.0 방식

GitHub은 the standard authorization code grant type 하고  웹 브라우저를 사용하지 않는 프로그램들을 위해서 OAuth 2.0 Device Authorization Grant를 지원한다.


 * authorization code grant type
 * OAuth 2.0 Device Authorization Grant for apps
   + 브라우저를 사용할 수 없는 앱용
 * Non-Web application flow
   + 테스트시에 간편하게 이용할 수 있는 방식



# Google

 * 구글 OAuth 2.0 API 공식 매뉴덜
   + https://developers.google.com/identity/protocols/oauth2/openid-connect

 * server flow 와 implict flow
> The most commonly used approaches for authenticating a user and obtaining an ID token are called the "server" flow and the "implicit" flow.
> The server flow allows the back-end server of an application to verify the identity of the person using a browser or mobile device.
> The implicit flow is used when a client-side application (typically a JavaScript app running in the browser) needs to access APIs directly instead of via its back-end server.
> This document describes how to perform the server flow for authenticating the user.
> The implicit flow is significantly more complicated because of security risks in handling and using tokens on the client side.   

 * server flow 에 대해서 자세한 설명은 https://developers.google.com/identity/protocols/oauth2/openid-connect?hl=en



### server flow의 요약
 *  Discovery-document URI 에서 authorization_endpoint, token_endpoint 를 구한다.



[OpenID Connect](https://developers.google.com/identity/openid-connect/openid-connect) describes just how to achieve sign-in using Google/ OAuth2.
