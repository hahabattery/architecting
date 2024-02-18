---
layout: default
title: Containers
description: summarizes the method of managing Kafka with containers.
nav_order: 3
grand_parent: Messaging
parent: Kafka
---

container로 kafka를 로컬에서 테스트시에 kafka설정을 아래처럼 했는데, kafka-topics.sh 에서 --bootstrap-server로 url 지정시에 domain url(kafka)를 찾지 못해서 에러가 발생했는데, 방법을 찾고 있는 중. 

```
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092
```


[Docker Compose 를 이용하여 Single Broker 구성하기](https://devocean.sk.com/blog/techBoardDetail.do?page=&query=&ID=164007&boardType=tags&searchData=Kafka&subIndex=&idList=)

[Kafka-UI Tool 을 이용하여 Kafka 관리하기](https://devocean.sk.com/blog/techBoardDetail.do?ID=163980) <- kafka 클러스터를 생성하는 내용 포함.
