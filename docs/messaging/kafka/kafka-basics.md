---
layout: default
title: Kafka Basics
nav_order: 3
grand_parent: Messaging
parent: Kafka
---

[UI for Apache Kafka](https://github.com/provectus/kafka-ui)

* kafka-ui를 이용하는 내용
  + https://devocean.sk.com/blog/techBoardDetail.do?ID=163980



 * consumer에서 같은 그룹으로 여러 클라이언트가 동작하는 경우에는 , partition을 각 클라이언트가 분배해서 처리하게 된다.


# Resources
### Interesting Use Case
* [How to use Apache Kafka to transform a batch pipeline into a real-time one](https://medium.com/@stephane.maarek/how-to-use-apache-kafka-to-transform-a-batch-pipeline-into-a-real-time-one-831b48a6ad85)

### Example - Twitter

 * Twitter Java Client
   * https://github.com/twitter/hbc

 * Twitter API Credentials
   * https://developer.twitter.com/


**The ElasticSearch Consumer gets data from your twitter topic and inserts it into ElasticSearch**
 * ElasticSearch Java Client:
   * https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.4/java-rest-high.html

 * ElasticSearch setup:
   * https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html
   * https://bonsai.io/


 * Create topic for test
   * kafka-topics.sh --zookeeper 127.0.0.1:2181 --create --topic twitter_tweets --partitions 6 --replication-factor 1

 * Run Kafka console consumer
   * kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic twitter_tweets



# Tips

* [파티션 키를 사용하여 특정 파티션으로 메시지 적재하기](https://devocean.sk.com/blog/techBoardDetail.do?page=&query=&ID=164096&boardType=tags&searchData=Kafka&subIndex=&idList=)
  + => 이 방법도 괜찮으려나? 일반적으로 카프카에서 producer가 동일한 파티션으로 입력하기 위해서는 메세지키를 이용하는 것으로 알고 있다.

* [Kafka SpringBoot Quick Start](https://github.com/schooldevops/kafka-tutorials-with-kido/blob/main/05.KafkaSpringBootSample.md)


# Configuration

### client configuration
 * configure producer:
   * https://kafka.apache.org/documentation/#producerconfigs

 * configure consumers:  
   * https://kafka.apache.org/documentation/#consumerconfigs



---
# Version Compatability

 * version compatibility
   + Because API calls is versioned, broker and client application have bi-directional compatibility.
   + old client(v1.1) can communicate with new broker(v2.0)
   + new client(v2.0) can communicate with old broker(v1.1)
