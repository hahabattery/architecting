---
layout: default
title: Overview
nav_order: 1
grand_parent: Messaging
parent: Kafka
---


RPC is Remort Procedure Call


# Basic Knowledges

### Protocol Buffers
Protocol Buffers is required because gRPC relies on it for data serialization and we are gonna write some Protobuf schemas during the course.

### gRPC VS REST
https://learn.microsoft.com/en-us/aspnet/core/grpc/comparison?view=aspnetcore-3.0

### Channel
HTTP2 통신을 위한 TCP Connection을 관리하는 객체


---
# 4 Types of API in gRPC
* Unary
* Server streaming
* Client streaming
* Bi directional streaming

```
service GreetingService {
  // Unary
  rpc Greet(GreetingRequest) returns (GreetingResponse);
  // Server streaming
  rpc GreetManyTimes(GreetingRequest) returns (stream GreetingResponse);
  // Client streaming
  rpc LongGreet(stream GreetingRequest) returns (GreetingResponse);
  // Bi directional streaming
  rpc GreetEveryone(stream GreetingRequest) returns (stream GreetingResponse);
}
```


# GRPC 에러 핸들링
* "grpc error handling"
  + https://www.vinsguru.com/grpc-error-handling/
