---
layout: default
title: Transaction
nav_order: 3
grand_parent: Architecture
parent: MicroService
---



# PDF

### A Toy Model of Distributed Transactions - medium.com.pdf

MSA환경에서  분산 트랜잭션 처리의 간단한 예.

WAL (write ahead log), idempotency, versioning, two-phase commit, distributed locks, multi-version

---

#  분산락
보통 redis를 이용해서 많이 한다. 접해본적은 없지만, ZooKeeper를 이용한 유스케이스도 많이 있는 듯 하다.

### MySQL을 이용한 분산락으로 여러 서버에 걸친 동시성 관리
https://techblog.woowahan.com/2631/


---
# two-phase Commit
2 Phase Commit 은 넓게보면 Distributed transactions 의 범주에 포함된다.

[Distributed Transactions and Saga Patterns](https://blog.knoldus.com/distributed-transactions-and-saga-patterns/)


Maybe consider a variation of two-phase commit: 

Make and commit your changes to the database first, then do your API calls. 

If the API calls fail, then make compensatory changes to the database(basically, update the database with the previous values). 

This had the virtue of not locking the database for any appreciable length of time, but gives you a method to keep your changes in sync across the DB and API.



1. Begin db transaction
2. Update the db, setting a status or a flag that means something like "processing".
3. Commit transaction
4. Perform the API call.
5. If the API call succeed, the process still in control, begin transaction, do your db updates, also update the status/flag to "done", commit transaction. All good.
6. If the API call fails, or the process fails non-gracefully, the situation remains "dirty", but you have an indicator (status) that something has failed (status value is "processing"), so you can perform some "offline" (scheduled, regular) cleanup, as per your context. If more complicated, you involve in help "semaphors" (applock in db, the one that cleans up automatically on db connection drop) to control concurrency.

This should be done after each API call involving updates. Therefore, make some generic code there and use it for every API call; address specificities additionally for each API where needed.



---
# XA

XA는 2PC(2 phase commit)을 통한 분산 트렌젝션 처리를 위한 X-Open에서 명시한 표준.

예를 들어 Oracle데이타베이스와 IBM DB2 데이타베이스간에 2단계검증을 통한 2PC를 보장하여 트렌젝션을 보장시켜주는 것.

등록 된 하나 이상의 데이터베이스 간에 2PC 트랜잭션이 보장되어야 할 때 XA datasource 사용

하나 이상이 데이터베이스를 접근하더라도, 굳이 트랜잭션이 보장되어야 할 필요성이 없다면 Non-XA-datasource 사용하면 됨


### 2PC 트랜잭션 수행 단계
begin -> end -> prepare  -> commit

글로벌 트랜잭션을 하려면 반드시 2PC를 해야만 한다.

글로벌 트랜잭션은 여러 리소스 사이에서 처리하는 작업이기 때문에 "분산" 트랜잭션(Distribute Transaction)이라고도 함. = XA

비 XA 데이터 소스는 자원 관리자 로컬 트랜잭션(RMLT)만을 지원할 수 있으나,

XA 데이터 소스는 로컬 트랜잭션 뿐만 아니라 2단계 커미트 조정을 지원할 수 있다.(?)

### 1PC 트랜잭션 수행 단계
begin -> end -> commit

트랜잭션에 참여한 리소스들이 서로 같은 리소스라고 확인이 되었을 경우에는 prepare가 필요없다

위와 같이 처리하는것을 1PC(one-phase-commit)이라고 부름.

이렇게하면 prepare를 안해도 되므로 트랜잭션의 처리 성능 향상에 도움을 줄 수 있다.

따라서 1PC는 트랜잭션 성능 향상(Transaction optimization)의 한 방법이다.

※ 주의할 것은 1PC도 글로벌 트랜잭션이다. 로컬 트랜잭션 아님.


### XA JDBC를 사용하는 이유
JTA를 사용하기 위함임
 
-> 데이터베이스 두개 이상을 사용하거나, XA스펙을 지원하는 자원일 경우 2단계 커밋 지원 -> 안정적 트랜잭션 처리


* JTA(Java Transaction APIs)
  + -> 단일 데이터베이스나 데이터베이스 여러개를 이용할 경우 트랜잭션을 제어하기 위한 목적으로 Java에서 지원하는 API.

* Springboot + JPA + JTA(Atomikos) 구성
  + https://www.4te.co.kr/894

* 분산 데이터베이스 환경에서 RoutingDataSource 사용 시 JTA를 이용한 트랜잭션 처리
  + https://d2.naver.com/helloworld/5812258


---
# Saga
Simple API for Grid Applications의 줄임말인데, Saga는 약자가 아니며 Long Lived Transactions를 의미하는 단어이다.

보상 트랜잭션 방식으로 자주 사용되는 방법이다.


 * https://docs.aws.amazon.com/prescriptive-guidance/latest/modernization-data-persistence/saga-pattern.html
>The application needs to maintain data consistency across multiple microservices without tight coupling.
>There are long-lived transactions and you don’t want other microservices to be blocked if one microservice runs for a long time.
>You need to be able to roll back if an operation fails in the sequence.



 * [Saga 패턴을 이용한 분산 트랜잭션 제어](https://github.com/hgs-study/payment-saga-pattern)
   + 블로그 주소 https://velog.io/@hgs-study/saga-1

 * [Saga 분산 트랜잭션 패턴](https://learn.microsoft.com/ko-kr/azure/architecture/reference-architectures/saga/saga)  <= MS가 제공하는 알기쉬운 설명!


### Saga - Choreography 방식
순차적으로 이벤트가 전달되면서 트랜잭션이 관리되는 방식이다.

이벤트는 RabbitMQ, Kafka와 같은 메시지 큐 미들웨어를 사용해서 비동기 방식 혹은 분산 처리 형태로 전달할 수 있다.

순차적으로 이벤트를 발행하면서 Commit을 한 후 중간에 에러가 터지면 취소 이벤트를 던지면서 각 서버의 데이터를 처리한다.

![](./doc/saga-choreography-flow-01.png)


### Saga - Ochestration 방식

Saga Ochestrator에서 각 서비스를 호출하여 트랜잭션을 관리하는 방식이다.

![](./doc/saga-ochestration-flow-01.png)

* 1. 주문이 처리되면 SAGA Ochestrator에게 트랜잭션을 시작할 것을 알린다.
* 2. Ochestrator는 결제 명령을 결제 서비스로 보내고 결제 완료 메시지를 응답받는다.
* 3. Ochestrator는 주문 준비 명령을 Order 서비스로 보내고 주문 준비 완료 메시지를 응답받는다.
* 4. Ochestrator는 배달 명령을 Delevery 서비스로 보내고 배달 처리 완료 메시지를 응답받는다.
* 5. 트랜잭션을 종료한다.
 

다음은 롤백 과정이다.

![](./doc/saga-ochestration-flow-02.png)

* 1. 재고부족으로 인한 주문 취소 메시지를 Ochestrator에 전달한다.
* 2. Ochestrator에서는 보상 트랜잭션으로 환불 명령을 결제 서비스로 보내고 환불 완료 메시지를 응답받는다.
* 3. 트랜잭션을 종료한다.

Ochestration-based SAGA는 최종적으로 데이터 무결성을 보장하게 된다.

Ochestration-based SAGA는 트랜잭션을 Ochestrator가 집중 관리해주므로 복잡도가 줄어들고, 구현 및 테스트가 용이하다.

반면, Ochestrator 추가로 더 많은 인프라 자원을 사용해야 한다는 단점도 있다.

모든 트랜잭션에 대해서 보상 트랜잭션을 만들어줄 필요는 없다. 당연하지만 읽기전용 혹은 항상 성공하는 단계의 트랜잭션의 경우는 보상 트랜잭션을 만들지 않아도 된다!


* aws step function은 분산 시스탬을 오케스트레이션 방식으로 관리할 수 있도록 해준다

---

# 카프카와 분산 트랜잭션 관련
piotrminkowski.com의 글이 엔지니어 입장에서의 설명이 담백하게 잘 정리되어있는 듯 하다.

[Kafka Transactions with Spring Boot](https://piotrminkowski.com/2022/10/29/kafka-transactions-with-spring-boot/)

[Distributed Transactions in Microservices with Kafka Streams and Spring Boot](https://piotrminkowski.com/2022/01/24/distributed-transactions-in-microservices-with-kafka-streams-and-spring-boot/)

[Deep Dive into Saga Transactions with Kafka Streams and Spring Boot](https://piotrminkowski.com/2022/02/07/deep-dive-into-saga-transactions-with-kafka-streams-and-spring-boot/)

