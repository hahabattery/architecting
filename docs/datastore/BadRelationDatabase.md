---
layout: default
title: Bad Relational Database
nav_order: 3
parent: Datastore
---

RDB이용시의 불편한 점들을 정리한다.

* 배너 정보를 저장하는 테이블 스키마를 고민하다가 든 생각
mongodb가 많이 사용되는 유스케이스를 살펴보자. json 형태로 데이터를 free하게 세팅할 수 있을거 같다. 하지만, 만약에 mongodb도 스키마 세팅을 해야지 사용해야하는 Feature가 있지않은가? ElasticSearch 같은 경우는 Schema를 세팅해야지 사용할 수 있는 기능이 많았던거 같다.
