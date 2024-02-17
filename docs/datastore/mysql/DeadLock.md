---
layout: default
title: Deadlock
nav_order: 3
grand_parent: Datastore
parent: MySQL
---


# MySQL의 InnoDB에는 lock의 종류

- Row -level lock
- Record lock
- Gap lock


###  Row-level lock

가장 기본적인 lock으로서 테이블의 row마다 걸리는 단위다. 여기서는 2가지 종류가 있다.

- Shared lock (S)
- Exclusive lock (X)

Shared lock(S) 은 read에 대한 lock이다. 일반적으로 select 쿼리는 lock을 사용하지 않고 DB를 읽는데 일부 select에서는 read 작업 수행 시 각 row 단위에 lock을 건다.

Exclusive lock(X)은 write에 대한 lock이다. select for update 나 update, delete 등 수정이나 삭제 등 쿼리를 날릴 때 row 단위에 lock을 건다.

그리고 lock을 거는 규칙이 있는데,

1) 여러 transaction이 동시에 한 row에 S lock을 걸 수 있다. 즉 여러 transaction이 동시에 한 row를 읽을 수 있다.

2) 문제는 s lock 상태에서 다른 transaction이 x lock을 시도할 수 없다는 것이다. 즉 update, delete 등을 할 수 없다.

3) 또한 x lock이 걸려있는 row에는 다른 transaction이 s lock, x lock 을 걸 수 없다. 다른 transaction이 수정하거나 삭제하고 있는 row는 읽기, 수정, 삭제가 모두 불가능하다.

​정리하자면 S lock의 경우 같은 row에 접근이 가능하지만 X lock이 걸린 row는 접근 불가능하며, S lock이 걸린 row에 X lock을 수행할 수 없다.

​
### Record lock

record lock은 row가 아니라 index record에 걸리는 lock이다. 여기에도 s lock 과 x lock이 존재한다.

대표적을 select ... for upate로 조회하여 lock을 거는 경우인데, 예를 들면 다음과 같이 쿼리를 조회했다고 하자

```sql
select id, payment from sales where id=10 for update;
```
이 상태에서 다른 transaction이 id가 10번인 row를 select, update, delete를 하려고 하면 대기를 한다.

```sql
update sales set payment=20 where id=10;
```
또는

```sql
select payment from sales where id=10;
```
동시성을 우려하여 이렇게 처리하는 경우가 있는데, 문제는 첫 번째 lock은 x lock이기 때문에 다른 transaction에서 select 나 update를 하지 못하고 첫 번째로 수행한 transaction이 commit이나 rollback을 하기 전까지 계속 대기 상태로 있는다.

한두 건이야 문제가 되지 않겠지만 동시에 다량의 데이터가 조회될 경우 굉장한 성능 저하를 실감할 수 있다.

​
### Gap lock

index record의 gap에 걸리는 lock이다. gap 이란 index 중 db에 실제 record가 없는 부분을 말한다.

예를 들어 Id 칼럼에 1, 5, 10이라는 값이 있고, 중간값이 비어있다고 가정하자. (2,3,4,6,7,8,9는 비어있다) 이때 다음과 같은 쿼리를 실행한다.

```sql
select id from sales where id between 1 and 10 for update;
```
이때 다른 transaction이 2,3,4,6,7,8,9 중 하나의 번호로 insert를 시도하게 될 경우 gap lock 때문에 대기 상태에 걸린다. 그래서 이전에 쿼리를 수행한 transaction인 commit이나 update를 수행하기 전까지 기다렸다가 종료되면 실해하는 것이다.

---
# Lock 과 Transaction

 * information_schema.INNODB_TRX
   * LOCK을 걸고 있는 프로세스 정보
 * information_schema.INNODB_LOCK_WAITS
   * 현재 LOCK이 걸려 대기중인 정보
 * information_schema.INNODB_LOCKS
   * LOCK을 건 정보

mysql 8.0 버전은 performance_schema 스키마에 저장된다.


 * 실행중인 쿼리 조회
   * SELECT * FROM INFORMATION_SCHEMA.PROCESSLIST WHERE USER = 'user' AND COMMAND <> 'Sleep';

### ISOLATION LEVEL(REPEATABLE-READ)

 * inno db 테이블

```
mysql> show create table procedure_test;
+----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table          | Create Table                                                                                                                                                                                                                                                                                |
+----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| procedure_test | CREATE TABLE `procedure_test` (
  `memid` varchar(20) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `address` varchar(20) DEFAULT NULL,
  `tel_no` varchar(20) DEFAULT NULL,
  `status` varchar(20) DEFAULT NULL,
  `rate` decimal(32,18) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

 * 트랜잭션을 시작해서 update를 시작

```
mysql> show variables like '%isol%';
+---------------+-----------------+
| Variable_name | Value           |
+---------------+-----------------+
| tx_isolation  | REPEATABLE-READ |
+---------------+-----------------+
1 row in set (0.00 sec)
mysql> SET @@session.autocommit=FALSE;
Query OK, 0 rows affected (0.00 sec)

mysql> update procedure_test set rate=rate+1 where memId='test01@naver.com';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```


 * 위의 트랜잭션이 커밋되지 않은 상태에서는 모든 레코드의 update가 락이 걸림

```
mysql> update procedure_test set rate=rate+1 where memId='test01@naver.com';
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
mysql> update procedure_test set rate=rate-1 where memId='test02@naver.com';
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction

```


 * 인덱스 추가후, 다시 트랜잭션에서 UPDATE

```
mysql> ALTER TABLE procedure_test ADD INDEX (memid);
Query OK, 4 rows affected (0.03 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> update procedure_test set rate=rate+1 where memId='test01@naver.com';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

*  다른 클라이언트에서 업데이트시도(인덱스가 다른 경우에는 락이 걸리지 않음)

```
mysql> update procedure_test set rate=rate+1 where memId='test01@naver.com';
ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
mysql> update procedure_test set rate=rate+1 where memId='test02@naver.com';
Query OK, 1 row affected (0.23 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```


---
# dead lock이 걸린 이유?

그럼 처음으로 돌아가서 mysql의 기본값인 REPEATABLE-READ를 해둔 상태에서 dead lock이 걸린 이유는 무엇일까?

비즈니스 로직을 따라가보니 insert select 문이 있었다. 스탬프를 저장하는 테이블과 스탬프 로그를 저장하는 테이블이 있는데, 스탬프의 상태가 변할 때마다 로그를 기록하게 되어있다. 일일이 객체를 만들어 저장했으면 문제없었겠지만, 쿼리로 한 번에 처리하기 위해 insert select를 사용했고, 그 순간 스탬프 테이블을 조회, 사용하는 다른 transaction과 부딪히게 되면 dead lock이 걸렸던 것이다.

그래서 dead lock이 걸릴 때 대안으로 READ-COMMITTED으로 변경하는 것도 이 이유라 할 수 있겠다.

Mysql에서 기본값이 REPEATABLE-READ으로 되어있다보니 개발을 어떻게 하느냐에 따라 다음의 에러가 발생할 수 있다.

```
ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction
```
 그래서 오라클과 마찬가지 단계인 READ-COMMITTED으로 설정하는 경우도 있다.
