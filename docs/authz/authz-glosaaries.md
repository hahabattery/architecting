---
layout: default
title: Authorization Server Settings Glossaries
parent: Authentication And Authorization
nav_order: 3
---

# JOSE
 JOSE = JSON Object Signining and Encryption

* JWT (JSON Web Token) : JWS or JWE
* JWS (JSON Web Signature) : 서버에서 인증을 증거로 인증 정보를 서버의 private key 로 서명한 것을 Token 화 한것.
* JWE (JSON Web Encryption) : 서버와 클라이언트 간 암호화된 데이터를 Token 화 한것.
* JWK (JSON Web Key) : JWE 에서 payload encryption 에 쓰인 키를 token 화 한것. (물론, 키 자체도 암호화되어 있죠.)

### JWS
JWS 구조는 header, payload, signature 로 나뉘며 header 와 payload 는 JSON Object 로 되어있고 signature 는 이 둘을 서명한 것이다. 각각에 대해 Base64  를 하고 . 으로 구분하면 이것이 바로 JWT 가 된다. 서명에 쓰인 키는 Token 을 생성하는 자 (주로 인증 서버) 의 private key 이다.

### JWS 를 이용하여 인증을 하는 매커니즘

1. 클라이언트 A 가 로그인
2. 서버에서는 payload 에 넣고 싶은 내용을 담은 후 (로그인한 사람의 정보, 접근 권한 등) 서명하고 Token 발행
3. 클라이언트는 다음 통신에서 항상 Token 을 함께 전달
4. 서버에서는 주요 통신에서 Token 을 확인. 내가 서명한 것이 맞는지 서명에 대한 validation 수행
5. 내가 한 서명이 맞고 인증 정보를 확인한 후 적절히 행동.


# HMAC
 * https://blog.jiniworld.me/173
   + HMAC에 대한 알기쉬온 설명 + 테스트코드

 * https://d2.naver.com/helloworld/318732
   + 단방향 암호화에 대한 설명

 * [HMAC을 이용한 REST API 인증 방법 모음](https://bcho.tistory.com/m/725)


# JWT의 특징

 * URL-safe
 * Self-contained

JWT는 독립적(Self-contained)이기도 하다. 페이로드 안에 민감정보(사용자의 정보)가 모두 들어있으면, 데이터스토어(RDB등)에 대한 조회작업이 줄어든다. 
그래서 민감한 정보는 넣지 않는것이 바람직하다.

### Claim
claim이라는 용어가 자주 나오는데, JSON형태의 "주고받는 정보"라고 한다. 

> Claim은 어떠한 주제에 대해 주장하는 정보(A piece of information asserted about a subject)다. name/value 페어로 구성된다. 
> 주제에 대해 주장한다는 말이 어색해 보이는데, asserted는 단언하여 주장하는 뉘앙스다. 그리고 JWT는 두 당사자(parties)에 관한 것이다. 
> 따라서 어떤 주제에 대해 확신을 가지고 설명을 하는 느낌이고, claim 또한 어떠한 주제에 대해 확신할 수 있는 정보를 담았다는 뉘앙스로 생각된다. 
> 예를 들어, 어떠한 토큰은 특정 주체가 발급했다고 확신할 수 있다는 식이다. claim은 암호화되거나 서명으로 무결성이 보장되기 때문이다.

##### Registerd Claim Names

[Registered Claim Names](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1)

 * iss
   issuer, 발급 주체
   
 * sub
   subject, JWT의 주제(제목). JWT의 내용은 일반적으로 sub에 대한 설명이다.
   sub를 단순히 제목으로 사용하기보다는, user ID와 같이 전체를 포괄하는 내용을 담아주는 용도로 많이 사용하는 듯하다.

 * aud
   Audience, 받는 주체
   
 * exp
   Expiration, 만료시간. NumericDate value를 포함하는 숫자여야 한다.

 * nbf
   Not Before, 토큰의 활성 시작 시간. 예를 들어 nbf가 내일이면 오늘은 해당 토큰을 사용할 수 없다. NumericDate value를 포함하는 숫자여야 한다.
   
 * iat
   Issued At, 토큰이 발급된 시간. age를 계산하는 데 사용할 수 있다. NumericDate value를 포함하는 숫자여야 한다.

 * jti
   JWT ID, JWT의 유니크한 ID값. 중복 값이 생성될 확률이 무시할 수 있는 정도로 낮은 방법을 택해야 한다. 토큰이 재사용되는 것을 막아주는 용도로 사용할 수 있다.



##### public, private calims
public은 충돌이 일어나지 않는 이름(Collision-Resistant Name)이어야 하고, private은 registered나 public이 아닌 claim



### 구조

##### JOSE Header

JOSE Header part는 JWT의 구성요소이기 때문에 JWS와 JWE 둘 다 사용한다. 주로 JWT Claims Set에 적용될 암호화 작업을 설명해주고 옵셔널하게 그 외의 속성이 들어간다.
JOSE Header에 들어가 있는 값이 JWS를 위한 것이면 JWT Claims Set이 JWS Payload가 되는 것이고, JWE를 위한 것이면 JWT Claims Set이 JWE에 의해 암호화된 plaintext가 된다.


##### JWS Payload

JWS인 경우, Claims Set이 들어감.
JWE안 경우, JWT Claims Set은 plaintext로 암호화된 JWE가 된다.


##### JWS Signature이다.



---
# JWS와 JWE

### JWS(JSON Web Signature)
Claim을 디지털 서명한다. 




### JWE(JSON Web Encryption)
Claim을 암호화한다.클라이언트의 claim데이터를 사용할 수 없다.

* https://datatracker.ietf.org/doc/html/rfc7516#section-3.3


# OAuth 2.0 vs OpenID Connect vs SAML
Remember that it isn’t a question of which structure an organization should use, but rather of when each one should be deployed. A strong identity solution will use these three structures to achieve different ends, depending on the kind of operations an enterprise needs to protect. Their use cases are as follows:

### OAuth 2.0
If you’ve ever signed up to a new application and agreed to let it automatically source new contacts via Facebook or your phone contacts, then you’ve likely used OAuth 2.0. This standard provides secure delegated access. That means an application can take actions or access resources from a server on behalf of the user, without them having to share their credentials. It does this by allowing the identity provider (IdP) to issue tokens to third-party applications with the user’s approval.

### OpenID Connect
If you’ve used your Google to sign in to applications like YouTube, or Facebook to log into an online shopping cart, then you’re familiar with this authentication option. OpenID Connect is an open standard that organizations use to authenticate users. IdPs use this so that users can sign in to the IdP, and then access other websites and apps without having to log in or share their sign-in information.

### SAML
You’ve more likely experienced SAML authentication in action in the work environment. For example, it enables you to log into your corporate intranet or IdP and then access numerous additional services, such as Salesforce, Box, or Workday, without having to re-enter your credentials. SAML is an XML-based standard for exchanging authentication and authorization data between IdPs and service providers to verify the user’s identity and permissions, then grant or deny their access to services.
