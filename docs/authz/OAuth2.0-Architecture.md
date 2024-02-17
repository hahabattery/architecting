---
layout: default
title: Architecture
description: The content covers handling multiple authorization servers in Spring Boot application.
parent: Authentication And Authorization
nav_order: 3
---


Handling multiple authorization servers is not easy.

# Problems
1. Developers have to know(understand) multiple Identity Providers.
2. Need to translate claims in code.
3. Users are not consolidated into one place.
   some user join through google, some user join through facebook etc...
   Application has to maintain a consolidated users table.
4. Architecture Problem
   1. Single Page Applications
      If we use single page applications to connect to these IDPs, we are somehow exposing the client IDs of all of these in the browser, which is too much exposure.
   2. Microservice problem
      The way this will work is that once the login is complete. On the bugtracker application side, the application UI has an access token and refresh token. The application can then simply call the microservice and pass the access token as part of the request.It's usually sent as part of the aurhorization HTTP header. 
      But consider what will happen if there are two IDPs, Keycloak and GitLab. The bugtracker UI, which is the bugtracker application here, has a handle to the access token from either Keycloak or GitLab. 
      So the bugtracker API would need to go to KeyCloak when the KeyCloak access token is passed. It will have to go to GitLab to verify the access token for GitLab.If it is a jot, then it has to pull the public keys from either of the two identify providers.
      If there are 5 or 6 of these social identity providers, the burden on microservices is way too much.
      In other words, we would have to do too much customization in our Spring Boot application to handle different IDPs on both the UI side and each one of these microservices.
      in short, these thing is probably fine if we use a monolith and the UI itself is in the backend(if we are not using single page application).
      But there is a better solution such as "identity brokers".
