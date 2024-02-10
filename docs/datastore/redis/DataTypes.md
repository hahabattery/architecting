---
layout: default
title: Redis Data Types
nav_order: 3
parent: Redis
---



# Sorted Set
### use case
* ranking
* first-come, first-served purchase 
 

### score
According to the [Redis documentation](https://redis.io/commands/zadd/), a sorted set score is:

the string representation of a double precision floating point number.

[A double-precision floating point number](https://en.wikipedia.org/wiki/Double-precision_floating-point_format)

allows the representation of numbers between 10−308 and 10308, with full 15–17 decimal digits precision.

### tiebreaker
Basically, Redis Rank (ZSet) sorts based on the score, and if there are tiebreakers, it sorts again based on the key. Redis does not provide support for other tiebreaker criteria, so it needs to be handled at the server application level.




