---
layout: default
title: Keycloak Basic Configuration
grand_parent: Authentication And Authorization
parent: Keycloak
nav_order: 3
---

# Authorization Configuration

기본적인 keycloak 에서 권한설정 방법은 다음과 같다.
- client scope 생성
- role 생성 해서 client scope에 할당 (mapping 설정을 통해서 생략 가능)
- client에 client scope를 연결


Keycloak에서 role을 생성하고 client scope와 연결 시킨다.

생성한 Role을 지정한 client scope에 할당할 수 있다. 이렇게 해야지 role을 가진 사용자별로 scope를 관리하도록 할 수 있다. 또 생성한 scope는 client에 연결시켜야 한다.

* 참고)
  + Token 정보에 aud 가 있는데 이거는 audience라는 항목으로 이 token을 어디쪽으로 보내야하는지에 대한 정보를 의미한다.예를 들어서 refresh token은 aud가 인증서버url로 세팅된다. 


