---
layout: default
title: Datastore Overview
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
