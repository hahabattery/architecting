---
layout: default
title: Architecture Patterns
nav_order: 3
parent: Architecture
---


# Transactional Outbox

* [Polling publisher](https://microservices.io/patterns/data/polling-publisher.html)
* [Transaction log tailing](https://microservices.io/patterns/data/transaction-log-tailing.html)

kafka와 같은 Message Broker를 이용해 다양한 메시지(이벤트)를 publish(발행) 하고, 그에 연관된 작업을 비동기적으로 처리하여 시스템을 통합하도록 처리한다.

이때 DB 트랜잭션을 실행한 뒤 연관 메시지를 Message Broker에 publish 하게 되는데, 때로 메시지 publish가 반드시 완료되어야 하는 경우가 있습니다.

만약 주문 기능이라면, 주문 발생후 쿠폰을 적용하고, 디털 상품인 경우는 상품을 지급하고, 주문 상태를 "완료"처리하는 DB 트랜잭션이 발생한다. 그리고 Message Broker에 주문 완료 이벤트 메시지를 퍼블리시한다. 하지만, 이 과정에서 DB와 Message Broker는 원자적인 처리가 불가능하다.

따라서 DB 상 주문 완료 처리가 되었더라도 Message Broker에 메시지를 publish 하는 데 실패할 수 있고, DB의 주문 완료 처리를 rollback 하기도 어렵습니다.

![](/images/image-Transactional-Messaging-2048x736.png)

Transactional Outbox 패턴은 Message Broker로 publish 하려는 메시지의 생성을 DB 트랜잭션에 포함시켜서 원자적으로 처리되게 하는 것을 의미합니다.

### 첫번째 로직
가장먼저 간단히 처리할 수 있는 로직은 다음과 같다.
DB 트랜잭션이 완료된 이후 Kafka에 메시지를 퍼블리시 하고, 실패한 메시지를 DLQ용 DB 테이블에 저장한 뒤 별도의 batch process에서 retry를 하는 방식이다.

이 방식은 다음과 같은 문제가 있을 수 있다.

* DB 트랜잭션과 메시지 publish를 원자적으로 처리할 수 없기 때문에, DB 트랜잭션이 완료되어도 메시지 publish를 보장할 수 없습니다.
* 일정 시간 간격으로 batch process가 실행되면서 retry가 되다 보니 신속하지 않았습니다.
* 메시지 간 publish 순서가 중요한 경우, 토픽의 경우 retry 되는 메시지가 늦게 publish 되면서 기대했던 순서가 뒤바뀔 수 있습니다.

### Polling Publisher
Polling Publisher 방식으로는 Outbox DB 테이블에 polling 하는 것만으로 publish 할 메시지를 가져올 수 있습니다. DB 트랜잭션이 실행되는 시점부터 publish 할 메시지 정보를 각 비즈니스 로직에서 생성하여 Outbox DB 테이블에 저장하기 때문입니다.


### Transaction Log Tailing 
Transaction Log Tailing 방식은 publish 할 메시지를 on-demand로 생성합니다. DBMS마다 트랜잭션이 처리되면 log(예를 들어 MySQL의 경우 binlog)를 생성하게 되는데, 해당 log에 대한 CDC(Change Data Capture)를 구현하는 것이죠.

먼저 Polling Publisher 방식은 전반적인 구조가 단순해서 구현하기 간편하지만, 비교적 높은 비용의 polling이 DB 부하로 이어질지도 모른다는 우려가 있었습니다.

반면 Transaction Log Tailing 방식은 polling 방식의 단점이 없는 대신, MySQL binlog에 대해 CDC를 구현하고 그 결과를 바탕으로 Kafka consumer에서 사용하는 메시지 포맷으로 데이터를 생성하는 작업이 필요합니다. 주로 직접 binlog에 접근하는 방식보다 Debezium과 같은 도구를 통하는 것이 권장되지만, CDC 도구를 학습하고 운영하는 것과 CDC 도구에서 생성하는 메시지의 schema 를 관리하는 데 적지 않은 비용이 들 것 같았습니다.


