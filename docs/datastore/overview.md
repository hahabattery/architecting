---
layout: default
title: Overview
description: It compiles general information regarding methods of storing data.
nav_order: 1
parent: Datastore
---

![](/images/datastore/목적에맞는데이터베이스사용과현대화.png)



---
# DB 설계관련 pdf
 * Database Performance at Scale(A Practical Guide)
   + https://link.springer.com/book/10.1007/978-1-4842-9711-7



---
# RDB
 * 정규화

 * Driving and Driven Table

 * Top 15 Ways to Kill MySQL Performance
   * https://www.youtube.com/watch?v=kxer0A23sQ4
   * => medium에 post


# Book

 * SQL Antipatterns


# PolyglotPersistence
알맞는 DB를 고르기

https://martinfowler.com/bliki/PolyglotPersistence.html


* Aurora
  + critical relational database loads with MySQL and PostgresSQL compatibility
* Amazon RDS
  + managed relational database services available in up to 7 popular engines
* DynamoDB
  + high throughput low-latency reads
  + single-digit millisecond performance, serverless, high throughput and storage
* DocumentDB(MongoDB)
  + fully managed JSON document database to operate critical document workloads (with MongoDB compatibility)
* ElastiCache
  + fully managed, Redis- and Memcached-compatible caching service
* MemoryDB for Redis
  + a durable database to scale to hundreds of millions of requests per second
* Neptune
  + fully managed database servie to run graph applications
* Timestream
  + fast, scalable, and serverless time-series database service that can store trillions of events per day
* QLDB (Quantum Ledger Database)
  + fully managed ledger database that provides a transparent, immutable and cryptographically verifiable transaction log
* Keyspaces(for Apache Cassandra)
  + scalable, highly available, and managed Apache Cassandra compatible database service which is NoSQL based


# Distributed Transaction

### DTM
 * 다기종 Database에서의 트랜잭션 관리에 대한 구현 예


 * https://medium.com/better-programming/how-to-implement-a-distributed-transaction-across-mysql-redis-and-mongo-9f6c7448b3b5
 * https://github.com/dtm-labs/dtm
   + 2 phase  message(?), Saga, Tc, Xa 지원


##### DTM 튜토리얼

 * https://en.dtm.pub/

# 분석용 데이터베이스

대시보드에 데이터를 어떻게 표시할 것인가를 한참동안 검토했었다. 가령 테이블에 어떤 데이터를 쌓고, 어떤 특정 타입 구분을 위해서 플래그를 세워서 관리하는 등의 내용이었다.
다양한 의견이 있었는데,

1. 과거데이터를 불필요하니까 표시하지 말자는 입장
2. 특정 데이터를 위한 RDB용 통계테이블을 새로 만들자는 입장

나는 대시보드나 통계정보를 위해서 새롭게  테이블을 정의해서 저장하는 것에 대해 논의를 하는 것이 ...

분석용 데이터베이스를 이용하는 것이 맞지 않나하는 생각을 했다.

최근의 adhoc 데이터를 생성하는 기능을 위해서 RDB를 이용하는 것은 유지보수측면(스키마 설계, 정규화를 고려한 데이터 접근등)에서도 리소스를 많이 들이게 된거 같다. 

**RDB는 비즈니스 측면에서 중대한 데이터(ex. 잔고데이터), 트랜잭션 처리가 필요한 경우에 사용하는 것이 맞을거 같다.**

* 유스케이스
  + AWS Aurora의 zeroETL을 이용

