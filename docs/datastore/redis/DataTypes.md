---
layout: default
title: Redis Data Types
nav_order: 3
grand_parent: Datastore
parent: Redis
---



# Sorted Set
### use case
* ranking
* first-come, first-served purchase 
 
### CLI Command

* member 추가하기

```
ZADD <key> <score> <member>
```

* member의 score 값 조회하기

```
ZSCORE <key> <member>
```

* score 작은 순서로 순위 조회
index시작은 0부터

```
$ ZRANGE <key> <시작> <끝>
```

* score 큰 순서로 순위 조회
index시작은 0부터

```
$ ZREVRANGE <key> <시작> <끝>
```

* score가 큰 순서대로 정렬했을 때 member 순위 조회하기

```
ZREVRANK <key> <member>
```






### score
According to the [Redis documentation](https://redis.io/commands/zadd/), a sorted set score is:

the string representation of a double precision floating point number.

[A double-precision floating point number](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)

allows the representation of numbers between 10−308 and 10308, with full 15–17 decimal digits precision.

### tiebreaker
Basically, Redis Rank (ZSet) sorts based on the score, and if there are tiebreakers, it sorts again based on the key. Redis does not provide support for other tiebreaker criteria, so it needs to be handled at the server application level.


10점 5점 5점 5점의 score를 가진 4명이 존재한다면  랭킹확인시 5점 3명은 2등으로 표시되길 원하기 때문이다.
이러한 문제를 해결하고자 reverseRangeByScore메서드를 사용할 수 있다. 이 메서드를 사용하면 원하는 score 범위의 value값을 원하는 갯수만큼 가져올 수 있는 기능을 한다. 하지만 위에서 Sorted Set은 score가 같을때 value값을 기준으로 정렬하기 때문에, 따라서 나의 점수와 같은 사람들 중 등수가 제일 높은사람의 등수를 반환할 수 있는 처리를 해서(zreverserank 함수 이용) 동일 순위를 구할 수 있다.