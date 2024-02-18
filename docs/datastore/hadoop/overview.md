---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: Hadoop
---

**Hadoop = HDFS + MapReduce**

### MapReduce
MapReduce는 코드가 복잡해서 짜기가 어렵다. Query로 MapReduce 대부분의 기능을 표현할 수 있다. SQL과 유사한 HiveQL로 데이터를 조회하는 등 MapReduce와 같이 처리할 수 있도록 하는 것이 Hive이다.

### Spark
Spark는 분산 시스템을 사용한 프로그래밍 환경으로 대량의 메모리를 활용하여 고속화를 실현하는 것이다. 인메모리상에서 동작하기 때문에,
반복적인 처리가 필요한 작업에서 속도가 하둡보다 최소 1000배 이상 빠르다.

최근에는 Hadoop + Spark의 연계가 하나의 공식이 되었다. 하둡의 YARN 위에 스파크를 얹고, 실시간성이 필요한 데이터는 스파크로 처리하는 방식으로 아키텍처를 구성하여 동작한다.
