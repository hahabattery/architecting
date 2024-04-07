---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: OpenSearch
---

[Amazon OpenSearch Service’s vector database capabilities explained](https://aws.amazon.com/ko/blogs/big-data/amazon-opensearch-services-vector-database-capabilities-explained/)


---
# What is it?
AWS Elasticsearch은 elasticsearch 7.10 버전을 포크해서 자체 유지 및 개발한 제품이다.

Open Distro 라는 사이트가 있는데, open distro for elasticsearch 즉, 오픈소스버전의 elasticsearch를 개발(배포?)하는 커뮤니티인데,
이 커뮤니티가 open search쪽으로 이동한 것으로 보임.


---
# 호환성
AWS Elasticsearch는 기본적인 사용법이 elasticsearch와 유사하나 앞으로 사용법에 차이가 벌어질 수밖에 없고 elasticsearch의 이후 버전이 적용되어있지 않다.

Elastic Cloud Service가 지원하는 기능들도 사용 불가능하다.

또한 AWS ES는 디폴트로 필요한 플러그인이 이미 설치되어 있고 자체적으로 플러그인을 따로 설치할 수는 없었다(2022년도 시점)


**[OpenSearch 로드맵](https://github.com/orgs/opensearch-project/projects/1)**

ElasticSearch의 경우는 생태계(spring data와 같은...)가 잘 구축이 되어있는거 같은데,

OpenSearch 같은 경우는 앞으로 어떤 방향성을 가지고 진행하려고 하는지 잘 모르겠다.



실제로 인덱싱만 잘 되있다고 해서, 개발하는 입장에서는 애로사항이 있을 수 밖에 없다... 어떻게 되려나...

=> 관련 라이브러리들이 지원이 안되지 않을까 했었는데, 쓸데 없는 걱정이었다. https://mvnrepository.com/artifact/org.opensearch 에서 보면 알 수 있듯이 많은 라이브러리들이 유지보수 되고 있는거 같다.


---
# 용어

### SQL VS OpenSearch

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


---
# Query DSL
 * 정리가 잘되있음
   + https://jangseongwoo.github.io/elasticsearch/elasticsearch_query_dsl/

Query DSL 이 상당히 복잡하고, 직관적이지 않은거 같다...

외우기보다는 최대한 자주 확인하는 수밖에 없음.




---
# AWS cloudsearch vs openSearch
cloudsearch라고  AWS에서 개발한 lucene이랑 solr 를 이용한 서비스인데, 최근 8년정도 업데이트가 없다. 아마 버려진 듯. 만약에 검색 서비스를 만든다고 하면, OpenSearch쪽이 더 나을 것이다.

비용이 ElasticSearch 에서 제공하는 Saas보다 1/3이긴 한데...

---
# 구축관련

[EFK Stack 구성하기 (Amazon OpenSearch + FluentBit + Kibana)](https://popappend.tistory.com/45)

### 워크샵

* SIEM on Amazon OpenSearch Service
  + https://github.com/aws-samples/siem-on-amazon-opensearch-service
  + opensearch를 이용해서 SIEM을 구현하는 예
  + cloudformation으로 별도 수정없이 완성된다고 함

* SIEM on Amazon OpenSearch Service Workshop
  + https://catalog.us-east-1.prod.workshops.aws/workshops/60a6ee4e-e32d-42f5-bd9b-4a2f7c135a72/en-US

* SIEM on Amazon OpenSearch Service Workshop (한국 리전에서 구축하는 예, 웨비나에서 사용)
  + https://catalog.us-east-1.prod.workshops.aws/workshops/2ff04db5-bb02-4208-b637-d54a352f7bc6/ko-KR

[OPENSEARCH LOG ANALYTICS WORKSHOP](https://sharkech-public-dev.s3.amazonaws.com/dev-opensearch-log-analytics/index.html)

[SIEM on Amazon OpenSearch Service Workshop](https://catalog.us-east-1.prod.workshops.aws/workshops/60a6ee4e-e32d-42f5-bd9b-4a2f7c135a72/en-US)

[GRAVITON2 AND AMAZON OPENSEARCH SERVICE](https://graviton2-workshop.workshop.aws/en/amazonopensearch.html)

[Amazon OpenSearch Service - AWS Observability Recipes](https://aws-observability.github.io/aws-o11y-recipes/aes/)

[LOGGING WITH AMAZON OPENSEARCH, FLUENT BIT, AND OPENSEARCH DASHBOARDS](https://www.eksworkshop.com/intermediate/230_logging/)

[Amazon OpenSearch Service로 검색 구현하기](https://catalog.us-east-1.prod.workshops.aws/workshops/de4e38cb-a0d9-4ffe-a777-bf00d498fa49/ko-KR)


---
# ElasticSearch -> OpenSearch 변경사항

### ElasticSearch

-> OpenSearch


### Kibana

-> OpenSearch Dashboards (20230323 웨비나 자료에 OpenSearch Dashboards 소개 자료 있다.)


### Logstash

Logstash OSS 버전이 있다고 함.


### ElasticSearch APM Server

 * Trace Analytics
   + Trace Analytics은 AWS의 ElasticSearch 설치시의 Kibana 설치시에 디폴트로 포함된다(일종의 플러그인?).

 * AWS Distro for OpenTelemetry(CNCF에서 만든 SandBox 프로젝트)
   + 이게 APM Server의 역활을 하는 것으로 보임

 * DataPrepper
   + OpenTelemetry Collector에서 수집한 데이터를 ElasticSearch/OpenSearch 의 document 형식으로 컨버팅해준다.

OpenTelemetry Collector에서 수집해서, DataPrepper에서 ElasticSearch 도큐멘트 형태로 변환하면, Trace Analytics를 이용해서 분석하는 형태인 듯하다.


[AWS Adds Distributed Tracing to Their Elasticsearch Service](https://opensearch.org/docs/latest/observability-plugin/trace/get-started/)



### ElasticSearch APM Agent

APM Agent는 오픈소스여서 Open Search에서 이용시 문제가 없어야 하지만,
이슈가 있는 듯 하다.



---
# Plugin

https://docs.aws.amazon.com/ko_kr/opensearch-service/latest/developerguide/supported-plugins.html

자체적으로 플러그인을 설치할수는 없으며 한글형태소분석기인 nori는 사용할 수 없다. 은전한닢은 사용할 수 있다.
