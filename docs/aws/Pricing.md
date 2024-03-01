---
layout: default
title: Pricing
description: 
nav_order: 5
parent: AWS
---


# Pricing
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}



* free티어 정보
https://aws.amazon.com/ko/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all

---
 * "클라우드는 자신의 서버를 사지 못하는 고객이 이용하는 서비스" <= 이런 논제의 글을 봤는데, 요점은 클라우드 서비스는 AWS나 GCP가 마케팅하는 방식일 뿐이다는 의미.
 * 스타트업 기업들이 AWS에 비용을 협상할 수 있나? <= 협상한 경우가 여러번 있는 듯하다.

---
# 인스턴스의 시간 당 사용량과 Data transfer 비용 확인 방법

 * AWS Cost and Usage Report 이용을 권유드립니다.

 * 우선 https://aws.amazon.com/ko/premiumsupport/knowledge-center/tags-billing-cost-center-project/?icmpid=cstokc  를 참조하셔서 특정 AWS 리소스의 태그를 설정하실 것을 권유드립니다. (오른쪽 상단에서 한국어로 변경하시면됩니다)

 * 태킹을 하신 후 https://console.aws.amazon.com/billing/home?#/preferences/tags  에서 Active 로 변경하시면됩니다.

 * AWS Cost and Usage Report 를 받아보시려면

 * On the https://console.aws.amazon.com/billing/home?#/preferences  에서 "Receive Billing Reports"를 선택합니다.
Detailed Billing Reports 아래 빌링 리포트를 받아보실 S3 버켓을 설정하시면됩니다. policy 는 아래에 언급한 것처럼 sample policy 처럼 설정하시면됩니다.
 * Specify an S3 bucket for reports to be saved in. NOTE: Storage for these reports is charged at normal, on-demand S3 rates.
 * After entering the bucket name, click "Sample Policy" and put that policy onto the bucket in question.
 * Check any reports that you'd like to use.


---
# EC2 Throttling The CPU (AWS는 CPU Cycle에 제한을 건다.)
 * From
   + https://www.freecodecamp.org/news/improve-aws-performance-without-spending-more-money/

AWS throttling servers because our CPU load was never exceeding 10% on the EC2 test instance.
EC2 t2.micro instances as an example, the baseline CPU utilisation for this is set at 10%.
The instance is constantly earning credits based on the number of vCPU's it has.


### The calculation for earning credits is

```
1vCPU * 10% baseline * 60 minutes = 6 credits per hour
```


### The calculation for spending credits if you are utilising at 15%

```
1vCPU * 15% CPU * 60 minutes = 9 credits per hour
```

So, if you are constantly running at 15%, your instance is losing 9 credits per hour. A t2.micro instance can only accrue a total of 144 credits.
Once those credits run out, your CPU usage will be throttled at 10% which is the baseline utilistaion.

Baseline Performance/vCPU 는 인스턴스 타입에 따라서 달라진다. 아래의 예는

# RDS IOPS (AWS는 RDS이 IOPS에 제한을 건다)
AWS limits your IOPS at the rate of 3 * (storage assigned to your EBS volume).
하지만 만약 20GB라고 했을 때는 100IOPS 를 얻는데, 이것은 기본이 100IOPS이기 때문이다.
Every time your RDS instance goes above 100 IOPS, however, you consume burst credits.
100 IOPS를 넘게 되면 burst credit을 사용하고, burst credit을 다 쓰게 되면 100 IOPS로 제한되기 때문에 느려지게 될 것이다.


###  RAM과 IOPS의 관계

AWS measures IOPS as the reads and writes to the hard disk itself.

It doesn't measure it as reads / writes to the buffer pool maintained by the innoDB engine in MySQL.

예를 들어서 db.t2.micro 의 경우에 1GB의 램에 buffer pool size는 250MB이고, db.t3.medium의 경우에는 4GB의 램에 buffer pool 사이즈가 2GB인데,

250MB와 같이 적은 용량에서는 buffer pool에 table scan을 위해서 큰 테이블을 로드하거나 할 때, 기존 buffer pool를 비우기 때문에 , 결과적으로 다시 디스크에서 로드해야 되기 때문에

IOPS가 많이 발생하게 된다.

그러면 어떤 쿼리가 buffer pool 사이즈를 210MB나 사용했던 것일 까.

```
EXPLAIN ANALYZE
SELECT `oc`.`oc_number` AS `ocNumber` , `roll`.`po_number` AS `poNumber` ,
`item`.`item_code` AS `itemCode` , `roll`.`roll_length` AS `rollLength` ,
`roll`.`roll_utilized` AS `rollUtilized`
FROM `fabric_barcode_rolls` AS `roll`
INNER JOIN `fabric_barcode_oc` AS `oc` ON `oc`.`oc_unique_id` = `roll` . `oc_unique_id`
INNER JOIN `fabric_barcode_items` AS `item` ON `item`.`item_unique_id` = `roll`.`item_unique_id_fk`
WHERE BINARY `roll`.`roll_number` = 'dZkzHJ_je8'
```

```
-> Nested loop inner join  (cost=468792.05 rows=582836) (actual time=0.092..288.104 rows=1 loops=1)
    -> Nested loop inner join  (cost=264799.45 rows=582836) (actual time=0.067..288.079 rows=1 loops=1)
        -> Filter: (cast(roll.roll_number as char charset binary) = 'dZkzHJ_je8')  (cost=60806.85 rows=582836) (actual time=0.053..288.064 rows=1 loops=1)
            -> Table scan on roll  (cost=60806.85 rows=582836) (actual time=0.048..230.675 rows=600367 loops=1)
        -> Single-row index lookup on oc using PRIMARY (oc_unique_id=roll.oc_unique_id)  (cost=0.25 rows=1) (actual time=0.014..0.014 rows=1 loops=1)
    -> Single-row index lookup on item using PRIMARY (item_unique_id=roll.item_unique_id_fk)  (cost=0.25 rows=1) (actual time=0.024..0.024 rows=1 loops=1)
```


Finally, on a whim I removed the BINARY function call in the query which I had put in to make sure that case sensitivity would not be an issue.
The resulting query execution plan was shocking:
```
-> Rows fetched before execution  (cost=0.00 rows=1) (actual time=0.000..0.000 rows=1 loops=1
```

### 문제와 해결책

https://stackoverflow.com/questions/71908085/why-does-removing-the-binary-function-call-from-my-sql-query-change-the-query-pl

---
# RI(Reserved Instance)
아래와 같은 설정 옵션이 있다.
* Upfront
  + 설결재
* Convertible
  + 같은 타입의 인스턴스와 교체할 수 있다. 다른 타입과 교환도 할 수 있다.


---
# Network
![](/images/aws/pricing-network-01.png)
![](/images/aws/pricing-network-02.png)
![](/images/aws/pricing-network-03.png)
