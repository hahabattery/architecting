---
layout: default
title: Aurora MySQL
nav_order: 1
grand_parent: Datastore
parent: Aurora MySQL
---


# QA

### 스토리지 복제본에 대해서

* 첫번째 질문
질문: Aurora MySQL를 생성하면 AZ당 스토리지의 복제본을 2개씩 만드는 것으로 알고 있습니다.Reader 인스턴스를 추가하면, 생성된 스토리지 복제본을 이용해서 디스크 I/O도 분산되는건가요?

답변: Aurora 스토리지는 단일 가상 볼륨인 클러스터 볼륨에 저장되고, 해당 볼륨은 3개의 AZ의 데이터 사본으로 구성됩니다. 스토리지의 복제 양은 DB인스턴스 수와는 관계가 없습니다.

자세한내용은 해당 내용참고부탁드립니다. https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.htm


* 두번째 질문
질문: Aurora MySQL를 생성하면 AZ당 스토리지의 복제본을 2개씩 만드는 것으로 알고 있습니다. Reader 인스턴스를 추가하면, Reader 인스턴스가 DB 조회시에 복제된 데이터 사본에서 조회해서 속도가 더 빨라질까 궁금합니다.

답변: 읽기전용 복제본에도 적절한 스토리지 유형을 선택할 수 있습니다. 성능을 위해서는 프로비저닝된 IOPS를 활용 하시면 좀더 개선된 속도를 받아 보실 수 있습니다.

해당부분에서 추가 확인이 가능하십니다. https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/USER_ReadRepl.html

=> 결국 원하는 답변은 얻지 못함. 매뉴얼을 더 확인해야 할 듯.


* rePost에 질문
질문:
Aurora MySQL creates 2 Storage Replicas in 3 AZs. Does creating an Aurora read instance take advantage of Storage Replica?

If the read instance takes advantage of Storage Replica, I think it's great in terms of disk I/O.

I'm curious if it works this way.

답변:
An Aurora replica actually connects to the same storage volume as the primary DB instance and supports only read operations.

Please take a look at the below documentation for more details: https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.html
