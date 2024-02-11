---
layout: default
title: OpenID
parent: Authentication And Authorization
nav_order: 3
---

 OpenID 는 OAuth2에 없는 사용자의 아이덴티티 정보를 가지고 있다.

![](/images/authz/OpenID-introduce01.png)

Access Token으로 Authorization을 처리하고, ID Token은 사용자의 인증정보를 가지고 있다.


![](/images/authz/OpenID-introduce02.png)

Access Token과 ID Token 두개의 토큰을 조합해서 IAM(Identity Access Management)가 구현 가능해진다.

# 플레이그라운드
동작확인

https://oauth.com/playground/index.html <= 하단에 OpenID Connect 선택
