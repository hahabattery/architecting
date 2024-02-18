---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: Elasticsearch
---


* queryDSL은 필요할 때마다 접해볼 필요가 있음.


# 고민
* elasticsearch는 검색에"만" 최적화 되어있다.
* 데이터 저장용도로 별도 DB를 추가 관리해야 된다.


# 리소스

배민 서비스에서의 ES 사용기
 * [검색을 위한 데이터 다루기](https://techblog.woowahan.com/2718/)
 * [실시간 인덱싱을 위한 Elasticsearch 구조를 찾아서](https://techblog.woowahan.com/7425/)
   + 이 글에서 ElasticSearch 내부 검색 매커니즘을 설명하고 있다.

 *[ELK 셋팅부터 알람까지](https://techblog.woowahan.com/2659/)


# 용어

### SQL VS ELasticSearch VS MongoDB

* SQL
  + Table
  + Row
  + Column
* ElasticSearch
  + Index
  + Document
  + Field
* MongoDB
  + Collection
  + Document
  + Field


# Query DSL
 * 정리가 잘되있음
   + https://jangseongwoo.github.io/elasticsearch/elasticsearch_query_dsl/

Query DSL 이 상당히 복잡하고, 직관적이지 않은거 같다...

외우기보다는 최대한 자주 확인하는 수밖에 없음.



# Sync 처리

 * [Keeping Elasticsearch in Sync](https://www.elastic.co/kr/blog/found-keeping-elasticsearch-in-sync)
   + 위 글의 정리
   + [엘라스틱서치에 데이터 싱크하기 ( 1 )](https://americanopeople.tistory.com/m/274)
   + [엘라스틱서치에 데이터 싱크하기 ( 2 )](https://americanopeople.tistory.com/m/275)
 * 위 글의 요약
   + 실시간으로 ES와 Datasource를 동기화 하는 것은 매우 비싸다.
   + bulk API가 아닌 요청을 매번 여러번 호출하면, ES 자원을 많이 사용하게되고, 클라이언트는 느려지게 된다.
   + > Lucene makes the tradeoff of fast and flexible reads at the expense of cheap writes.
   + ES document가 update되면 예전 segment안의 document는 '삭제됨'으로 mark하고, 새 document가 buffered되고 보통 segment까지 새로 만들게 된다.
   + analyzer가 document에 대해서 새로 돌아야 되기 때문에 CPU사용량이 증가한다. segment가
   + 인덱스의 세그먼트 수가 과도하게 증가하거나 세그먼트에서 삭제된 문서의 비율이 높으면 이전 세그먼트에서 문서를 복사하여 여러 세그먼트를 새로운 단일 세그먼트로 병합합니다(이전 세그먼트는 삭제됨).
   + 예를 들어서 단순히 게시물의 조회수를 업데이트한다고 해도, rdb와는 다르게 document의 삭제/제거, analyzer의 re-run이 발생한다.
   + 중복 엘리먼트를 허용하지 않는 queue를 이용해서, 모아서 한번에 배포한다. 새로운 RDB의 테이블 전체를 full-reindex하는 경우에도 큐를 사용하는것이 비효율적으로 보일 수 있지만, ES 언제나 빠른 결과를 내준다.
   + ES는 1초안에 검색이 가능하게 해준다(refresh_interval참조).
   + 튜토리얼 성격이라서 뒷부분은 생략, 필요할 때마다 참조하자.


### 관련 리소스

 * https://github.com/go-mysql-org/go-mysql-elasticsearch


# ElasticSearch Query DSL
 * https://jangseongwoo.github.io/elasticsearch/elasticsearch_query_dsl/
   + => 정리가 잘되있음


# 성능관련

[Elasticsearch from the Bottom Up, Part 1](https://www.elastic.co/kr/blog/found-elasticsearch-from-the-bottom-up)



# Spring Data ElasticSearch
ElasticSearch의 "Query DSL"은 RDB의 querydsl 과는 다른 의미이다.
querydsl.com은 Lucene에 대해서만 지원이 되는 듯하고 ElasticSearch는 지원하지 않고 있다.
Spring Data ElasticSearch를 사용하면 되는 듯하다.

하지만, OpenSearch에 대한 지원이 안되는 분위기? 인거 같아서 사용해도 되나 싶다.
