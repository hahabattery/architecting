---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: MySQL
---

# Timestamp vs Datetime

[The DATE, DATETIME, and TIMESTAMP Types](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)

> MySQL converts TIMESTAMP values from the current time zone to UTC for storage, and back from UTC to the current time zone for retrieval. (This does not occur for other types such as DATETIME.) 

* 이러한 값의 변경 처리는 application과 MySQL의 타임존이 다른 경우에도 처리되는데, 둘다 발생하면 중복되어서 처리될 텐데, 그런 문제는 없나? 하긴 Spring JPA이용시에는 Java의 LocalDateTime 클래스를 사용하면, timestamp가 아니고 datetime으로 처리되었다.

### 값의 범위
* Datetime
  + 1000-01-01 00:00:00부터 9999-12-31 23:59:59까지 지원
* Timestamp
  + 1970-01-01 00:00:01부터 2038-01-19 03:14:07까지 지원
  + Index가 더 빠르게 생성된다.


