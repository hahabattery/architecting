---
layout: default
title: Index
nav_order: 3
grand_parent: Datastore
parent: MySQL
---




# jojoldu의 DB 성능 관련 글

 * 인덱싱관련 여러 세부사항이 잘 들어있음

### mysql 인덱스 정리 및 팁

 * https://jojoldu.tistory.com/243

### 커버링 인덱스
 * 1. 커버링 인덱스 (기본 지식 / WHERE / GROUP BY)
   + https://jojoldu.tistory.com/476?category=761883
 * 2. 커버링 인덱스 (WHERE + ORDER BY / GROUP BY + ORDER BY )
   + https://jojoldu.tistory.com/481?category=761883


데이터베이스 covering index 적용.
=> 광범위한 성능 개선효과

### mysql IN절을 통한 성능 개선 방법

 * https://jojoldu.tistory.com/565?category=761883

### IN쿼리가 인덱스를 타지 않는 현상
 * https://jobc.tistory.com/216

### Update Subquery 성능 비교 (ver.5.6)

 * https://jojoldu.tistory.com/522?category=761883

### mysql 대용량 테이블 스키마 변경하기

 * https://jojoldu.tistory.com/244?category=761883

### mysql where in (서브쿼리) vs 조인 조회 성능 비교 (5.5 vs 5.6)

 * https://jojoldu.tistory.com/520?category=761883

### 인덱스 컨디션 푸시다운
 * https://jojoldu.tistory.com/474?category=761883

---

# MySQL 인덱스 관련 내용 정리

### 복합 인덱스

RDBMS는 쿼리 사용시 한 테이블에는 1개의 인덱스만 적용 된다.

테이블간의 Join하는 경우는 복합 컬럼 인덱스를 이용해야 하는데, 예를 들면, 조인 컬럼이 user_id이고, 조건 컬럼이 course_id, deleted_at인경우 user_id, course_id,deleted_id 로 생성해야 한다.

### Driving / Driven table

테이블간의 join시에 Optimizer에 의해서 먼저 호출된 테이블을 드라이빙 테이블이라고 하고, 이후의 테이블들은 드리븐 테이블이라고 한다. 드리븐 테이블에서는 인덱스 탐색 작업과 스캔 작업이 드라이빙 테이블에서 읽은 레코드 건수만큼 반복하기 때문에, 드리븐 테이블의 읽기 비용이 더 크다.

성능에 가장 큰 영향을 주는 요소는 인덱스와 레코드 건수이다. 이것을 Optimizer가 이 기준으로 동작을 한다고 생각하기 쉬운데, 그런데 테스트해보면 Optimizer의 동작(드라이빙 / 드리븐 테이블의 선택)이 다를 수도 있기 때문에, 테스트가 필요하다.

MySQL버전과 실행 SQL에 의해서도 차이가 있는 것 같다.

중요한 것은 explain으로 미리 동작을 테스트해보는 것이다. 



# Left Join 시에 on 과where 의 차이점

SQL문에서 ON과 WHERE절의 차이점은 JOIN을 할때 필터링하는 시점이 다르다

on은 JOIN을 하는 시점에 JOIN 대상을 필터링하고, 

where는 JOIN을 하고 난 후에 그 목록을 필터링을 한다.

그래서 left join시에 on을 사용하면 null인 컬럼이 조회된다.

