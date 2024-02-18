---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: MongoDB
---

 * https://docs.mongodb.com/manual/tutorial/getting-started/

 * mongodb의 사용법은 거의 유사하다. (예를 들어서, console명령과 nodejs 드라이버의 사용법은 비슷하다)
 * mongodb nodejs driver 로 검색해서 reference document 페이지로 이동하면, API Document를 참조할 수 있다.


 * Config Files
   * https://docs.mongodb.com/manual/reference/configuration-options/
 * Shell (mongo) Options:
   * https://docs.mongodb.com/manual/reference/program/mongo/
 * Server (mongod) Options
   * https://docs.mongodb.com/manual/reference/program/mongod/

 * insert update delete 메소드들의 document
   * https://docs.mongodb.com/manual/reference/method

```
테스트를 위한 MongoDB atlas의 접속정보
test20201215 / DS8023d8a
```

# 몽고DB의 적용 케이스
> 대규모의 데이터를 처리해야 하는데 RDBMS는 성장 한계가 있기 때문에, 일관성과 무결성을 버리고 더 빠른 읽기 성능과 수평확장이 가능한 DB가 필요할 때 , 사용함.

웹 서비스가 발전하면서 데이터 무결성을 버리면서까지 더 많은 데이터, 빠른 성능, 수평 확장이 필요한 데이터베이스가 필요해졌다. 그런 요구 사항으로 인해 MongoDB가 탄생했다.



### 트랜잭션 처리가 꼭 필요하지 않은 데이터 저장시 이용
 * RDB같은 경우는 데이터를 1000건 넣는데, 서버가 죽거나 해서 500건만 들어가는게 말이 안되는 거 같지만,
 * 말이되는 영역이 있을 수 있다.(개인적인 추천기능)
 * 결재데이터라든데, 회원 정보같은 것은 MySQL을 이용하고, 나머지 것들은 몽고DB를 이용하는 식으로 사용하고 있다.
 * 추천 데이터, 큐레이션 데이터 같은 정보를 몽고DB에 저장하면 편하다.

### 로그성 데이터
 * 비즈니스모델에 따라서 스키마리스로 저장해야되는 경우라든지 예를 들어서 게임 이용자의 액션로그를 저장하는 경우는 액션로그가 형태를 고정할 수 없는데, 이런 로그성 데이터를 몽고DB에 저장하였다.

 * 타게팅 세일즈를 위해서 대량의 데이터를 넣고, 필요한 데이터를 특정 사용자에게 개인화된 정보를 보여주기 위해서 사용함.

 * FLO 보관함이나 간단한 로그성 데이터를 넣음.

 * 생각보다 서비스를 하는데 트랜잭셔널 한 데이터가 생각보다 많이 않은데 이런 서비스에 몽고DB를 사용하면 편하다

# Best Practice
[6 Rules of Thumb for MongoDB Schema Design](https://www.mongodb.com/blog/post/6-rules-of-thumb-for-mongodb-schema-design)

=>https://www.bearpooh.com/164 요약
* 불가피한 문제가 없다면 Document에 포함해라.(Embedded 방식)
* 특정 Document에 직접 접근할 필요가 있다면 분리해라.
* 배열이 지나치게 커져서는 안된다. 데이터가 크다면 one to many로 나눠라. 배열의 밀도가 높다면 분리해라.
* 애플리케이션에서 Join하는 것을 두려워하지 말아라. 대신 index 지정과 데이터 모델링이 중요하다.
* 비정규화는 Join하는 비용(읽기)이 분산 된 데이터를 찾고 갱신하는 비용(쓰기)보다 비싸면 고려해라.
* MongoDB의 데이터 모델링은 어플리케이션의 데이터 접근 패턴이 결정한다.


### 배열에 대한 개선

[Model One-to-Many Relationships with Embedded Documents](https://www.mongodb.com/docs/manual/tutorial/model-embedded-one-to-many-relationships-between-documents/)

배열은 포함한 원소가 많아지면 처리 속도가 느려질 수 있다. 또한 통합하다 보면 불필요한 필드가 포함되어 큰 Document가 되는 단점이 발생한다.

이러한 경우 레퍼런스 방식을 참고하여 서브셋 패턴으로 Document를 분리한다.


# MongoDB와 비교해서 MySQL(RDB)과의 차이점
### 성능상의 차이점
MySQL, PostgreSQL, MongoDB의 성능을 완벽하게 비교하는 것은 불가능할 것이다. 각자의 use case에 따라, 사용 환경에 따라 성능은 달라질 수 있기 때문이다. 다만 일반론적으로 비교해보면 다음과 같다.

* 비정형 데이터(unstructured data)를 다루는 경우 MySQL, PostgreSQL 대비 MongoDB가 유리할 수 있다.
* 데이터가 매우 큰 경우 (normalization 때문에) 여러 테이블을 참조해야 하는 MySQL, PostgreSQL 대비 MongoDB가 유리할 수 있다.
* JSON 데이터를 다루는 경우 MySQL, PostgreSQL 대비 MongoDB가 유리할 수 있다.
* 트랜잭션 처리가 중요한 경우 MongoDB 대비 MySQL, PostgreSQL이 유리할 수 있다.
* 단순한 쿼리만 사용하는 경우 PostgreSQL 대비 MySQL이 유리할 수 있다.
* 복잡한 쿼리를 사용하거나 대규모 데이터를 읽고 써야 하는 경우 MySQL 대비 PostgreSQL이 유리할 수 있다.
* update 쿼리가 많은 경우 PostgreSQL 대비 MySQL이 유리할 수 있다.



### 단점
 * 서비스가 커지고 트래픽이 많아지다 보니, 스케일업에 한계가 있다.

 * MySQL에는 HA솔루션이 MMM이 있는데, 운영부담이 크다. 하지만, 몽고DB는 자체 내장된 HA기능이 있어서 편하다.

### 장점




# 몽고DB의 장점

 * 중간에 와일드 타이거로 바뀌면서 인덱스 아키텍쳐가 기존의 RDB와 유사해져서, 문제시 튜닝 포인트나 원인을 파악하기 쉬워졌다.
 * 3.0의 와이어드 타이거 버전부터 로우레벨 락킹이 지원되면서, 많은 부분이 해소가 되었다.
 * DDL 작업시에 RDB는 서버 점검시에 서비스에 애로사항이 생기는데, 그 부분에서 스키마리스는 좋은 거 같다.

### 스키마리스
 * 기획미팅 -> 모델링을 하는데, RDB에서는 스키마의 정규화를 어떻게 할 것인거가 중요했는데
 * 몽고DB는 API의 응답을 주는 형태대로 저장을 하면 된다(스키마리스)

### 스케일 아웃
 * 서비스 부하에 따른 걱정을 하지 않게 된다, 필요하면 샤드를 늘리는 해결책이 있다.
 * 또한, 몽고DB는 자체 HA기능을 내장하고 있다.


# 몽고DB 도입시 고려할 사항

 * 도입시에 몽고DB에 익숙해지는 시간은 필요하다.
 * MQL같은 부분은 SQL과 형태가 다르기 때문에, 러닝커브가 있다.


# 몽고DB 적용시의 팁
 * 스몰스타트를 하고,샤딩이 그렇게 유연하지 않을 수 있기 때문에, 샤디드 클러스터보다는 리플리카셋 사용이 낫을 거 같다.
 * 서비스에서 메인 스트림에서 벗어난 일부서비스에 먼저 적용하는게 낫다.


# 몽고 DB의 단점

### 조인
 * 간단하게라도 조인이 되면 좋겠다(물론 룩업이 되기는 하지만) WAS입장에서는 조인이 안되면 몽고DB에 라운드트립이 여러번 발생할 수 있다.
 * 또, 어그리게이션같은 경우는 빠른 서비스의 경우에는 사용하기가 어렵다.

### 병렬 실행
 * MySQL이 최근에 패러럴 프로세싱 기능이 생겼다.
 * 데이터를 덤프나, 마이그레이션시에 몽고DB에는 아직 이런 기능이 없다.

### TTL 이용시 단편화
 * TTL 인덱스를 사용하면 데이터파일이 단편화가 발생하게 된다. 단편화 기능을 보완하기 위한 파티셔닝 기능이 몽고DB에는 없다.


# 한글 검색
몽고DB의 경우 한글은 default 설정이면 인덱싱이 되지 않는다고 한다(https://www.facebook.com/groups/krmug/posts/858480694306079/).

* [mongodb에서-n-gram-full-text-search-이용하기](https://rastalion.me/mongodb%EC%97%90%EC%84%9C-n-gram-full-text-search-%EC%9D%B4%EC%9A%A9%ED%95%98%EA%B8%B0/)
* [Full Text Search Index](https://rastalion.me/mongodb-index-5-full-text-search-index/)


# 웨비나(21-06-24)

 * 질문
  + 1. 제가 사용해본 몽고디비는 document의 row가 스키마가 달라도 mongodb는 다 받아주는데 이를 배제 할려한다면 어떻게 할 수 있는지가 궁금합니다.
  + 2. 설계하는게 너무 어렵습니다. rdbms는 확실한 관계를 가지기는데 몽고디비는 이런게 애매해 보입니다. 이를 임베디드 하게 된다면 조회시 find 쿼리에서 속도 이슈가 없을지도 궁금합니다.

 * 답변
   + 1. MongoDB에서는 Data Governance 측면에서 Schema 유효성 검사를 수행할 수 있습니다.
   + 참고 :https://docs.mongodb.com/manual/core/schema-validation/
   + 2. MongoDB는 스키마 설계시 데이터를 저장하는 기반이 아닌, 조회에 유리한 스키마 설계가 필요합니다. 임베딩과 리퍼런싱은 조회 요건에 따라서 , 또는 데이터의 성격에 따라서 사용자가 선택해야 하는 부분입니다.


 * 질문
   + 중요 콜렉션과 아카이빙 콜렉션을 두고 중요 콜렉션에서 삭제한 도큐를 아카이빙 콜렉션으로 저장하는 비지니스 로직을 때 몽고의 TX를 사용하지 않으면서 정합성을 유지 하기 위한 방안이 있을까요?
 * 답변
   + writeConcern 과 retryable Write 를 통해서 durability 를 보장 할 수 는 있지만, 두 가지 작업을 all or nothing 형태로 처리 할려면 TX를 사용하셔야 합니다. 참고로 Atlas 플랫폼에서는 Online Archive 라는 서비스를통해서 컬렉션 데이터를 S3 로 실시간으로 넘기는 서비스를 제공하며 단일 API를 통해서 양쪽의 데이터를 동시에 조회가 가능합니다.


 * 질문
   + 물류 업종인데 실시간 설비 움직임 그리고 로그 DB처리 하려고 하는데 Nosql로 적합할지 궁금합니다.
 * 답변
   + MongoDB를 많이 사용하는 영역 중 하나가 IoT 및 RealTime Analytics 입니다. 실제 센서 및 위치 데이터를 실시간 트랙킹 하는 부분에 있어서 많이 사용하고 있습니다


 * 질문
   + 몽고db에서 추천하시는 몽고db 장애 예방을 위한 모니터링 방안에는 어떤것이 있을까요 ?
 * 답변
   + Atlas 라는 몽고디비에서 제공하는 클라우드 서비스를 사용하시면 운영 비용을 최소화 하실 수 있습니다.
   + Atlas 에는 상용 APM 과 Integration을 할 수 있어서 자동화된 모니터링이 가능합니다.


# Mongoose

### Query

 * Query and Projection Operators 로 검색하면, 검색시의 여러가지 방법을 찾을 수 있다.




# Index
네이버 컨텐츠검색과 몽고DB 2019.pdf <- 이게 인덱스 관련 내용이 맞이 들어있다.
 * 모든 콜렉션에는 _id 의 index가 생성되며,
 * findById() 함수형태는 매우 빠르게 동작한다.
