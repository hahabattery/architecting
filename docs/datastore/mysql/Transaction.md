---
layout: default
title: Transaction
nav_order: 3
grand_parent: Datastore
parent: MySQL
---


# InnoDB MVCC (multi-version concurrency control)

* https://dev.mysql.com/doc/refman/8.0/en/innodb-multi-versioning.html

트랜잭션 관리에서 MVCC 를 적용한다.



# Transaction Isolation Level

트랜젝션 격리 수준이란?

특정 트랜잭션이 다른 트랜잭션에 의해 변경된 데이터를 볼 수 있도록 허용할지 여부를 결정하는 것이다.

​

격리수준은 4개로 나뉜다

- READ-UNCOMMITTED

- READ-COMMITTED

- REPEATABLE-READ

- SERIALIZABLE

​

아래로 내려갈수록 고립 정도가 높아진다

고립정도가 높아진다는 의미는 단순하고 엄격해짐을 의미한다. SERIALIZABLE 수준잉 되면 SELECT를 하는것만으로도 잠금(LOCK)을 설정되어 다른 트랜잭션에서 이 레코드를 전혀 변경하지 못하게 된다. 데이터 정합성은 좋아지겠지만 문제는 성능저하가 높아진다는 것이다.

​

일반적인 온라인 서비스에서는 READ COMMITTED와 REPEATABLE READ를 사용한다.

오라클의 경우 기본값은 READ COMMITTED이, MYSQL의 경우 REPEATABLE READ가 기본값이다.

​

​

ISOLATION LEVEL을 조회하는 방법은 다음과 같다

SHOW VARIABLES like 'tx_isolation';
​

​

각 설정에 따른 것을 좀더 상세히 알아보자

​

### READ-UNCOMMITTED

다른 트랜잭션의 변경내용이 Commit이나 Rollback과 상관없이 보여지는 상태다. Dirty Read를 유발하며 데이터 정합성 문제가 많 때문에 RDBMS에선 격리수준으로 인정하지 않을 정도다. 일반적으로 거의 사용하지 않는다.

​

​

### READ-COMMITTED

Commit이 완료된 데이터만 다른 트렌잭션에서 조회할 수 있다. 아직 Commit되기 전이라면 다른 트랜잭션은 undo영역에 있는 이전 값을 참고하여 보여준다.

Oracle에서 기본으로 사용되고 있는 격리수준이다.

​commit 된 데이터만 읽는 것을 허용하는 레벨.

읽기의 경우, SELECT 문장이 수행되는 동안 해당 데이터에 Shared Lock이 걸리고 SELECT가 완료되면 Lock이 해제됨.

쓰기의 경우, 데이터를 변경하는 동안, 다른 트랜잭션은 데이터를 변경할 수 없고, wait하게 됨.

정리하면, 읽기는 SELECT, 쓰기는 트랜잭션 단위로 lock걸림.

​

### REPEATABLE-READ
트랜잭션에서 SELECT시에 해당 데이터에 Shared Lock을 걸고 데이터의 Snapshot을 생성한다. 이후 동일 트랜잭션 내의 SELECT는 Snapshot에서 읽게 됨.

> A consistent read means that InnoDB uses multi-versioning to present to a query a snapshot of the database at a point in time. The query sees the changes made by transactions that committed before that point of time, and no changes made by later or uncommitted transactions.
> ...
> If the transaction isolation level is REPEATABLE READ (the default level), all consistent reads within the same transaction read the snapshot established by the first such read in that transaction. You can get a fresher snapshot for your queries by committing the current transaction and after that issuing new queries.

![](/images/mysql/repeatable-read-figure-1.png)

스냅샷을 생성하는 동안에는 shared lock을 걸기 때문에 이 시간이 오래걸리면 "Lock wait timeout exceed"가 발생하게 된다.

회피방법으로는 DB설정이나 프로그램 소스에서 isolation level을 READ COMMITED로 낮추는 것.

모든 데이터에 shared lock이 걸리며, 다른 사용자는 해당 영역 데이터 수정이 불가능하다.

Binary Log를 가진 Mysql 장비에서는 최소 이 단계의 격리 수준 이상을 사용함을 권장한다.

Commit하지 않는다면 undo 영역에 백업된 데이터를 보여준다. 만약 트랜잭션을 종료하지 않는다면 undo영역이 무한정 커질 수 있다. 하지만 실제로 영향을 미칠정도로 오래 지속되는 경우는 잘 없다.

참고로 1번 트랜잭션이 update문을 수행하고 아직 commit하기 전이라 할때, 2번 트랜잭션이 접근되어 update문을 수행할 경우 쓰기잠금을 할 수 없는데, 이유는 undo에 있는 영역에서 조회해온 데이터이기 때문이다.

* REPEATABLE_READ mode에서 트랜잭션 안에서 Update문을 실행할 시에는 row lock이 걸리게 된다. 즉, TxA가 특정 row에 대해 업데이트를 수행한 상태에서 같은 row에 대하여 TxB가 업데이트를 시도했을 때, TxB는 TxA가 commit되기 전까지 lock을 대기하는 상태가 된다. 앞서 SELECT에서는 snapshot을 가지고 같은 transaction 안에서는 해당 snapshot을 사용한다고 했는데, 업데이트 하려는 row가 다른 트랜잭션에 의해 업데이트된 상태임에도 이전 snapshot을 사용하게 된다면, 정합성이 깨지게 된다. 이에 따라 UPDATE가 된 경우 해당 row에 대한 consistent read는 refresh되고, latest state에 해당 UPDATE를 반영한 결과가 보여지게 된다. 이는 MySQL 공식문서에 아래와 같이 명시되어있다.

> A consistent read means that InnoDB uses multi-versioning to present to a query a snapshot of the database at a point in time. The query sees the changes made by transactions that committed before that point of time, and no changes made by later or uncommitted transactions. The exception to this rule is that the query sees the changes made by earlier statements within the same transaction. This exception causes the following anomaly: If you update some rows in a table, a SELECT sees the latest version of the updated rows, but it might also see older versions of any rows. If other sessions simultaneously update the same table, the anomaly means that you might see the table in a state that never existed in the database.

* 같은 맥락에서 TxA가 특정 row에 대해 업데이트를 수행한 상태에서 같은 row에 대해서 TxB가 select를 시도했을 때는 select는 읽기 잠금(shared lock)을 얻지 못하기 때문에 대기하게 된다.

​

### SERIALIZABLE

거의 사용하지 않는 설정

단순 SELECT를 할때에도 공유 잠금(읽기 잠금)을 획득해야 하며, 다른 트랜잭션은 그 레코드를 변경할 수 없다.



---
# Undo log
undo 로그에 대해서 정리된 글이 있어서 간추려보았다.

### Repeatable Read Isolation
At the heart of InnoDB's isolation spectrum lies the Repeatable Read level. Within a given transaction, consistent reads are anchored to the initial snapshot established by the first read operation. This intrinsic behavior extends its reach to a series of plain SELECT statements conducted within the confines of a single transaction. In other words, the consistency is maintained even among these SELECT statements.

### Undo Logs
Undo logs, in their essence, serve as the safety net for transactional changes. They record the original values of data that transactions might alter, ensuring that the system can roll back changes if the need arises. These logs are instrumental in achieving the core tenets of the ACID properties.

**The life cycle of an undo log mirrors the journey of a transaction**

* Initiation
  + When a transaction kicks off, an undo log is born. It captures the initial state of data that the transaction interacts with, setting the stage for consistency.
* Data Evolution
  + As the transaction progresses and modifies data, the undo log diligently records the data's previous states. These historical records provide the means to undo changes.
* Consistent Reads
  + In the realm of Repeatable Read isolation, the undo log shines. When a transaction reads data, the undo log facilitates consistent reads by offering the original data state at the transaction's inception.
* Rollback Assistance
  + If a transaction hits a dead-end and must be rolled back, the undo log takes the lead. It restores the original values, erasing the traces of the transaction's journey.
* Transaction Closure
  + Upon a transaction's successful completion, its undo log entries fade into obsolescence. They make way for future transactions, ensuring the system operates efficiently. This involves a Purge Process that you could read more about it here.

### The Role of Transaction ID in Maintaining Consistent Reads (Snapshots)
The concept of the transaction ID is pivotal in preserving consistency despite the aforementioned challenge. In practical terms, the transaction ID acts as a safeguard, enabling the system to discern between the data that the ongoing transaction can view and the data altered externally by other transactions. This ensures that even in the Repeatable Read isolation level, snapshots are correctly managed, and the transaction perceives the most relevant data at any given moment.

InnoDB uses transaction IDs to establish snapshots for each transaction. These snapshots play a crucial role in achieving the desired isolation level for a transaction. For instance, in the Repeatable Read isolation level, the transaction sees a consistent snapshot of data throughout its execution, even if external changes occur.

Also the transaction ids are crucial for resolving conflicts in scenarios where multiple transactions attempt to modify the same data concurrently. The transaction with the higher transaction ID typically takes precedence, ensuring that changes are applied in a well-defined order.

### Conclusion
At the end I suggest selecting the correct isolation level for InnoDB MySQL database based on your requirements and if you are sure that the repeatable read is the best option for your situation, consider the consistent reads and snapshots in your designs and assumptions. Also it’s a good practice to not have long transactions. If you want to acquire the lock, the best location for doing that is not in the middle of the transaction (do it before entering the transaction).

---
# MySQL Transaction Isolation level: REPEATABLE_READ Mode에서의 Lock 이해
* https://jyeonth.tistory.com/32
  + Isolation level이 Repeatable Read시의 동작에 대해서 자세히 설명해주고 있다.
