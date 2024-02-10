---
layout: default
title: Spring Data
grand_parent: Springs
parent: Spring JPA
nav_order: 4
---

# Paging

### JPA Paging 이 필요할 때 사용하려면

```java
public List<Board> findByBnoGreaterThanOrderByBnoDesc(Long bno,Pageable paging)
```

코드는 기존과 동일하지만, 파라미터에 Pageable이 적용되었고 Collection<> 에서 List<> 로 변경됨.

* Pageable 인터페이스가 적용되는 경우는 리턴타입이 Slice, Page, List 타입이다.
