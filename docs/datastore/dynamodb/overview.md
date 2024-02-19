---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: DynamoDB
---

# 리소스
 * Watch! 유데미 유료 강좌(이미 구입함)
   + https://www.udemy.com/course/dynamodb/learn/lecture/10119222?src=sac&kw=dynamodb#overview

 * AWS re:Invent 2021 - DynamoDB deep dive: Advanced design patterns
   + https://www.youtube.com/watch?v=xfxBhvGpoa0

 * AWS re:Invent 2021 - Data modeling with Amazon DynamoDB
   + https://www.youtube.com/watch?v=yNOVamgIXGQ

 * Best practices for designing and architecting with DynamoDB
   + https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html

 * DynamoDB용 실습
   + https://aws.amazon.com/ko/getting-started/hands-on/?getting-started-all.q=dynamodb&getting-started-all.q_operator=AND&getting-started-all.sort-by=item.additionalFields.sortOrder&getting-started-all.sort-order=asc&awsf.getting-started-category=*all&awsf.getting-started-level=*all&awsf.getting-started-content-type=*all

 * dynamodb 강좌 보기
   * https://www.udemy.com/course/aws-serverless-a-complete-introduction
   * https://www.udemy.com/course/aws-serverless-a-complete-guide/learn/lecture/12400926?start=150#questions

 * DynamoDB Immersion Day
   * AWS account 팀에 연락하면, 개발자 위주로 대면교육을 진행
 
 * DCAD
   * Immersion Day를 진행했거나, 비슷한 수준일 경우 진행 가능.

 * [Spring Boot에서 Repository로 DynamoDB 조작하기 (1) – 설정부터 실행까지](https://techblog.woowahan.com/2633/)
 * [Spring Boot에서 Repository로 DynamoDB 조작하기 (2) – Gradle을 활용해 실행 & 테스팅 환경 구축](https://techblog.woowahan.com/2634/)

---
# 특징
 * DynamoDB는 볼륨이 방대하게 커지더라도 빠르고 일관된 응답시간을 가진다. 이 이유는 DynamoDB가 테이블마다 Primary Key로 Partition Key를 가지고 있는데, Partition Key 때문에 응답시간이 일관됩니다.
 * DynamoDB는 Nosql의 MongoDB와 비교가 많이 되고 있는데, 개인적으로 가장 큰 차이점은 DynamoDB는 Fully managed라는 점인 것 같습니다. 둘 다 대용량 데이터에 굉장히 능한 데이터베이스이지만 MongoDB는 꾸준한 유지/관리가 필요하기 때문에 회사 입장에선 개발자 리소스를 꾸준하게 사용할 수밖에 없습니다. 하지만 DynamoDB는 AWS가 제공하는 Serverless NoSQL이기 때문에 NoSQL 엔지니어가 없어도 대용량 트래픽 처리 가능한 어플리케이션을 개발할 수 있고 개발자 리소스를 꾸준히 사용하지 않아도 된다는 이점이 있습니다.
 * 각 기업의 사업이 확장되면서 글로벌 비즈니스를 시작하는 기업들을 위해 글로벌 테이블을 제공합니다. 손쉽게 다른 Region으로 테이블을 복제할 수 있습니다.
 * DynamoDB는 테이블 크기와 관계 없이 몇 초안에 백업을 완료합니다. 또한 모든 백업은 자동으로 암호화되며, 손쉽게 검색 가능하며 명시적으로 삭제될 때까지 보존합니다. 또한 특정시점복구(PITR)이라는 기능을 활성화 하면 사용자가 명시적으로 비활성화 할 때까지 최근 35일 중 원하는 시점으로 테이블 복원이 가능합니다.
 * Auto-Scaling 기능 탑재
   + DynamoDB로 들어오는 워크로드에 따라서 처리량(RCU / WCU)을 Auto-Scaling 지원합니다.
 * 데이터 자동 복제
   + DynamoDB는 기본적으로 1개의 Region에 3개의 복제본을 만들어 놓기 때문에 한 곳에서 장애가 나더라도 가용성을 유지할 수 있는 특징이 있습니다.
 * High Performance
   + 빠르고 일관된 응답시간(Single-digit millisecond latency) 제공하고 있으며, 사실상 무제한 쓰로틀링과 무제한 저장 용량을 가지고 있습니다.

### 기술적 배경
 * open source, cross platform runtime environment for server-side application
 * Built on the Google Chrome V8 javascript engine
 * Event-driven, non blocking I/O model
 * JavaScript On the server


---
# DynamoDB 구조
DynamoDB는 기본적으로 Schemaless 테이블이며, Primary Key로는 파티션 키(Partition Key)와 정렬키(Sort Key)를 가지고 있으며 그 외 속성들인 Attributes로 구성되어 있습니다.

RDBMS에선 데이터 한 줄을 row라고 읽었다면 DynamoDB에선 item이라고 표현합니다.

### Partition Key

파티션 키는 Primary키로써 테이블 생성 시 필수 입력 사항이며, 파티션 키 수만큼 DynamoDB를 파티셔닝해서 들어오는 데이터를 각 파티션에 나눠서 적재하게 됩니다.

위 테이블은 장르 별로 파티션 키를 나누었습니다. 장르 같은 경우에는 카디널리티가 낮기 때문에 각 파티션에 많은 데이터들이 적재하게 됩니다. 위 그림과 같이 클래식이라는 장르에 많은 요청이 몰리게 된다면 "핫 파티션"이 발생하여 모든 파티션에 읽기/쓰기 성능에 영향을 주게 됩니다. 그렇기 때문에 파티션 키는 카디널리티가 높은 속성으로 지정하고 데이터가 파티션 고르게 분산하도록 설계하는 것이 좋습니다. (ex. 고객ID, 물품ID 등)

DynamoDB는 테이블 볼륨이 방대해지더라도 항상 일관된 응답시간을 제공해준다고 위에서 말씀드렸습니다. 그 이유는 파티션 키 내부에 해시 함수를 가지고 있기 때문에 요청 받은 파티션 키를 파라미터로 Hash Value를 도출하여 파티션이 결정되기 때문입니다. 그래서 테이블이 아무리 커지더라도 파티션 키를 인자로 Hash Value를 도출하여 빠른 속도로 해당 데이터에 접근하기 때문에 일관된 응답 시간을 제공할 수 있습니다.

> 파티션 키 연산은 “Equal” 연산만 가능합니다

### Sort Key
정렬키는 Primary Key이지만 테이블 생성 시 선택 입력 사항입니다. 주로 선택적, 범위 조회를 위해 사용하며, 관계 모델링에서 1:n관계와 n:m 관계를 모델링할 수 있습니다.

![](../../images/datastore/dynamodb/11_sort_key.png)

위 그림은 AWS 공식 문서에서 권유하는 정렬 키 모범 사례입니다. 정렬 키로 선택/범위 연산이 가능하기 때문에 데이터를 계층적으로 설계하여 해당 계층에 맞는 데이터를 가져올 수 있습니다. 이렇게 적용하면 지리적 위치를 큰 범위부터 작은 범위로 계층적으로 나열하기 때문에 country부터 neighborhood 까지의 데이터를 효과적으로 범위 쿼리할 수 있게됩니다.

> 정렬키는 선택, 범위 연산인 >,>=,<,<=,begin_withs,between 연산이 가능합니다

---
# Index
> DynamoDB는 Real World에서 데이터를 접근하기에 Primary Key(PK + SK)만으로는 부족하기 때문에 보조 인덱스(Secondary Index)를 추가적으로 제공합니다. 보조 인덱스는 LSI (Local Secondary Index)와 GSI(Global Secondary Index) 두 가지로 나뉘어집니다.

### GSI (Global Secondary Index)
![](../../images/datastore/dynamodb/dynamodb-index-02.png)


partition key, sort key까지 자유롭게 어떤 항목이든지 가져와

테이블을 생성한 후 원하는 조건으로 쿼리를 하여 가져오는 방식입니다.

Primary Key가 자동으로 포함됨.

따라서 정말로 원하는 모양의 테이블을 자유롭게 만들 수 있는 방식입니다.

최대 20개 까지 생성가능.

GSI는 따로 용량에 대한 제한이 없습니다.

 * Partition Key를 필수 설정하고, Sort Key는 선택 사항
 * 테이블 생성 후에도 생성/삭제 가능
 * 용량 제한 없음
 * 테이블 외 인덱스 데이터 따로 저장
 * 테이블 외 인덱스에서 RCU / WCU 따로 사용
 * **Eventual consistent read만 가능**
 * GSI 사용 권장

### LSI (Local Secondary Index)

![](../../images/datastore/dynamodb/dynamodb-index-01.png)

partition key를 fix한 상태에서(반드시 파티션키가 포함된다), 이 외의 컬럼에 대해서 Range Key = sort key로 주어 값을 가져오는 것으로,

메인 테이블에서 고민하는 것이 아니라 새롭게 내가 원하는 조건의 쿼리를 하기 위한 테이블을

Secondary Index로 생성하여서 원하는 조건으로 쿼리를 하여 가져오는 방식입니다.

단, LSI 는 용량이 10GB로 제한되어 있습니다.

 * 테이블과 동일한 Partition Key를 사용하며, Sort Key 지정
 * 테이블 생성 시에만 생성 가능하며, 삭제 불가능
 * 용량은 10GB로 제한
 * 파티션 내 테이블 데이터와 함께 저장
 * 테이블의 RCU / WCU 같이 사용
 * **Eventual Consistent Read와 Strong Consistent Read 선택 가능**
 * LSI 사용은 최대한 지양
   + LSI는 파티션 키를 테이블의 파티션 키와 동일하게 설정해야하기 때문에 두 파티션 키가 같은 데이터를 바라보고 연산한다는 점에서 메리트가 떨어지고, 특히나 용량 제한이 있어서 테이블 볼륨이 늘어날수록 해야하며 추가적인 생성과 삭제가 불가능하기 때문입니다.


### LSI와 GSI의 차이점

![](../../images/datastore/dynamodb/dynamodb-index-03.png)

### Eventual Consistent Read와 Strong Consistent Read
DynamoDB는 리전당 3개의 복제본을 가지고 있는데, 이중에서 가장 빠른 응답을 가지는 복제본을 사용할 수 있다!

Aurora MySQL같은 경우는 복제본을 백업 용도로만 사용하기 때문에, 속도와는 관련이 적다.

* Event Consistent Read
Eventual Consistent Read는 응답 속도는 빠르지만 최근 완료된 쓰기 작업 결과가 반영되지 않아 최종적인 데이터를 가져오지 않는다는 특징이 있습니다.


* Strong Consistent Read
Strong Consistent Read는 응답 속도는 느리지만 최근 완료된 쓰기 작업 결과가 반영되기 때문에 실시간 데이터에 민감한 비즈니스일 경우 Strong Consisntent Read와 Eventual Consistent Read를 적절히 섞어서 사용하면 됩니다. 

(※ Strong Consistent Read는 GSI를 사용할 수 없기 때문에 PK,SK, LSI를 사용해야하는 점 유의하시길 바랍니다)


---
# Data Modeling

RDB는 엔티티를 설계하고, 접근시에는 SQL 조인을 해서 사용할 수 있지만,

NoSQL인 DynamoDB에서는 접근하는 패턴에 따라서 미리 조인된 형태로 저장할 필요가 있다.


https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-data-modeling/steps.html


# DynamoDB를 사용하기에 맞지 않는 상황

 * when your access patterns are not clearly defined (which may result in costly refactoring)
 * when multi-item or cross table transactions are required
 * when complex queries and joins are required
 * when real-time analytics on historic data is required


### Like 검색은 사용할 수 없다.

Another item to be aware of with DynamoDB, is that you cannot search values with the LIKE operator as you can in SQL. You can only query string contents with the **begins_with** function. 



---
# DynamoDB관련 추가 리소스

### Emily Shea
 * AWS에서 서버리스 관련한 작업을 하고 있음
 * Repository: github.com/em-shea/vocab
 * Blog: emshea.com
 * serverlessland.com

 * dynamodb사용 관련 글
   * https://emshea.com/post/part-1-dynamodb-single-table-design



---
# Pricing
DynamoDB 과금 방식으로는 사용한 읽기/쓰기 처리량대로 납부하는 온디맨드 방식과 읽기/쓰기 처리량을 미리 할당하는 방식인 프로비저닝 방식이 있습니다. 
프로비저닝 방식을 사용할 때, 읽기/쓰기 처리량을 사용하지 않아도 그 금액만큼 과금을 해야하는데 Auto-Scaling을 활용하면 유휴 처리량을 절약하여 사용한 만큼만 과금하기 때문에 과금 상 이점을 얻을 수 있습니다.


### Provisioned Capacity

초당 읽기 / 쓰기 수를 지정함.

자동 조절 옵션도 지원함.

### On Demand

미리 사용량을 지정하지 않고, 사용량에 맞춰서 DynamoDB 워크로드가 확장 / 축소가 일어남


---
# DAX (DynamoDB Accelerator)
DynamoDB를 위한 가용성이 뛰어난 완전 관리형인 메모리 캐시로써, 초당 수 백만 개의 요청에서도 DynamoDB 테이블의 읽기 속도를 최대 10배까지 가속화할 수 있습니다.

캐싱 서비스(DAX)지원을 통해서 DynamoDB 비용을 절감하고 어플리케이션 처리하는 캐싱로직을 제거함으로써 데이터 일치와 복잡도 감소할 수 있습니다.

**현재 서울리전을 지원하지 않음**

---
# 비동기 처리
NativeCloud 컨셉에 맞게 최소한 인스턴스 Spec으로 자원 효율성을 얻는 Reactive Programming 지원합니다. (https기반 Non-blocking / spring webflux, nodejs)

---
아래는 기본적인 DynamoDB의 내용

![](../../images/datastore/dynamodb/dynamodb-01.png)
![](../../images/datastore/dynamodb/dynamodb-02.png)
![](../../images/datastore/dynamodb/dynamodb-03.png)
![](../../images/datastore/dynamodb/dynamodb-04.png)
![](../../images/datastore/dynamodb/dynamodb-05.png)
![](../../images/datastore/dynamodb/dynamodb-06.png)
![](../../images/datastore/dynamodb/dynamodb-07.png)
![](../../images/datastore/dynamodb/dynamodb-08.png)
![](../../images/datastore/dynamodb/dynamodb-09.png)
![](../../images/datastore/dynamodb/dynamodb-10.png)
![](../../images/datastore/dynamodb/dynamodb-11.png)
![](../../images/datastore/dynamodb/dynamodb-12.png)
![](../../images/datastore/dynamodb/dynamodb-13.png)
![](../../images/datastore/dynamodb/dynamodb-14.png)
![](../../images/datastore/dynamodb/dynamodb-15.png)
![](../../images/datastore/dynamodb/dynamodb-16.png)
![](../../images/datastore/dynamodb/dynamodb-17.png)
![](../../images/datastore/dynamodb/dynamodb-18.png)
![](../../images/datastore/dynamodb/dynamodb-19.png)
![](../../images/datastore/dynamodb/dynamodb-20.png)
![](../../images/datastore/dynamodb/dynamodb-21.png)
![](../../images/datastore/dynamodb/dynamodb-22.png)
![](../../images/datastore/dynamodb/dynamodb-23.png)
![](../../images/datastore/dynamodb/dynamodb-24.png)
![](../../images/datastore/dynamodb/dynamodb-25.png)
![](../../images/datastore/dynamodb/dynamodb-26.png)
![](../../images/datastore/dynamodb/dynamodb-27.png)
![](../../images/datastore/dynamodb/dynamodb-28.png)
![](../../images/datastore/dynamodb/dynamodb-29.png)
![](../../images/datastore/dynamodb/dynamodb-30.png)
![](../../images/datastore/dynamodb/dynamodb-31.png)
![](../../images/datastore/dynamodb/s3-dynamodb-diff.png)


