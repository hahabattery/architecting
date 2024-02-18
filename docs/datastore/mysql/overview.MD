---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: MySQL
---




---
# Schema


 * 초기 스키마 생성서 자주 사용하는
  
```SQL
create database batch_db;
create user sdev@'%' identified by 'sdev123!@#';
create user sdev@localhost identified by 'sdev123!@#';
grant all privileges on batch_db.* to sdev@'%';
grant all privileges on batch_db.* to sdev@'localhost';
flush privileges;
```


 * 테이블 맨앞에 auto increment 쿼리 추가하기

```SQL
ALTER TABLE `TableName` ADD COLUMN `seq` INT AUTO_INCREMENT UNIQUE FIRST;
```

---
# Constraints

### Foreign Key


테이블 기준 확인

```SQL
select * from information_schema.table_constraints where table_name = '테이블명';
```

데이터베이스 기준 확인

```SQL
select * from information_schema.table_constraints where constraint_schema = '데이터베이스명';
```


---
# Procedure


 * 기본 명령

```SQL
SHOW PROCEDURE STATUS;
SHOW CREATE PROCEDURE curtest;
```

 * 테스트데이터

```SQL
DROP TABLE procedure_test IF EXISTS;
CREATE TABLE procedure_test (
   memid VARCHAR(20),
   age INT,
   address VARCHAR(20),
   tel_no VARCHAR(20),
   status VARCHAR(20)

) ENGINE = InnoDB ROW_FORMAT = DEFAULT;

insert into procedure_test (memid, age, address, tel_no, status ) VALUES ( 'test01@naver.com', 20, 'London city, England', '0102981910', 'Canceled') ;
insert into procedure_test (memid, age, address, tel_no, status ) VALUES ( 'test02@naver.com', 30, 'Seoul city, gangnam', '0102981910', 'Resolved');
insert into procedure_test (memid, age, address, tel_no, status ) VALUES ( 'test03@naver.com', 40, '2nd quater, Mars', '0102981910', 'Disputed');
insert into procedure_test (memid, age, address, tel_no, status ) VALUES ( 'test04@naver.com', 50, '3nd sector, Pluto', '0102981910', 'Disputed');
alter table procedure_test add rate decimal(32,18) default null;
update procedure_test set rate=1929.29124982;
```


 * 변수 할당

@ is used as a prefix denoting stored procedure and function parameter names, and also variable names


```SQL
DELIMITER $$
CREATE PROCEDURE proctest01()
BEGIN
  DECLARE total_products INT DEFAULT 0;

  SELECT COUNT(*) INTO total_products FROM procedure_test;
  END $$
DELIMITER ;
```

* 다중 변수 + dynamic SQL

```SQL
CREATE TABLE table1(
  column1 VARCHAR(255) DEFAULT NULL,
  column2 VARCHAR(255) DEFAULT NULL,
  column3 VARCHAR(255) DEFAULT NULL
);

INSERT INTO table1 VALUES
  ('1', 'value1', 'value2'),
  ('2', 'value3', 'value4');

DELIMITER $$
CREATE PROCEDURE procedure1(IN Param1 VARCHAR(255), OUT Param2 VARCHAR(255), OUT Param3 VARCHAR(255))
BEGIN
  SET @c2 = '';
  SET @c3 = '';
  SET @query = 'SELECT column2, column3 INTO @c2, @c3 FROM table1 WHERE column1 = ?';
  PREPARE stmt FROM @query;
  SET @c1 = Param1;
  EXECUTE stmt USING @c1;
  DEALLOCATE PREPARE stmt;
  SET Param2 = @c2;
  SET Param3 = @c3;
END$$
DELIMITER ;

-- Call procedure and use variables
SET @Param1 = 2;
SET @Param2 = '';
SET @Param3 = '';
CALL procedure1(@Param1, @Param2, @Param3);
SELECT @Param2, @Param3;
+---------+---------+
| @Param2 | @Param3 |
+---------+---------+
| value3  | value4  |
+---------+---------+
```

 * INOUT test

```SQL
DELIMITER $$
CREATE PROCEDURE set_counter(INOUT count INT(4),IN inc INT(4))
BEGIN
 SET count = count + inc;
END$$
DELIMITER ;

SET @COUNTER = 1;
CALL set_counter(@counter, 1);
CALL set_counter(@counter, 1);
CALL set_counter(@counter, 5);
SELECT @COUNTER;
```


 * 커서 심플 예제

```SQL
DELIMITER $$
CREATE PROCEDURE curtest()

BEGIN
  DECLARE done INT DEFAULT FALSE;
  DECLARE vRowCount INT DEFAULT 0 ;
  DECLARE vMemID varchar(20) DEFAULT '';


  -- 커서로 만들 데이타 값들
  DECLARE cur1 CURSOR FOR SELECT memid FROM procedure_test ;

  -- 커서가 마지막에 도착할 때의 상태값
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

  -- 커서를 연다.
  OPEN cur1;

  -- Loop 가 돌아간다.
  read_loop: LOOP

  -- 커서로 만들어진 데이타를 돌린다.

  FETCH cur1 INTO vMemID ;


	SELECT vMemID;
    -- 커서가 마지막 로우면 Loop를 빠져나간다.

    IF done THEN

      LEAVE read_loop;

    END IF;
    SET vRowCount = vRowCount +1 ;

  END LOOP;

  SELECT vRowCount ;

  -- 커서를 닫는다.
  CLOSE cur1;

  END $$
DELIMITER ;
```

 * 다중값 반환 예제

```SQL
DROP PROCEDURE get_order_by_cust;
DELIMITER $$

CREATE PROCEDURE get_order_by_cust(
 IN memid varchar(20),
 OUT canceled INT,
 OUT resolved INT,
 OUT disputed INT)
BEGIN
 -- canceled
  SELECT count(*) INTO canceled
  FROM procedure_test
  WHERE memid = memid AND status = 'Canceled';

 -- resolved
  SELECT count(*) INTO resolved
  FROM procedure_test
  WHERE memid = memid AND status = 'Resolved';

 -- disputed
  SELECT count(*) INTO disputed
  FROM procedure_test
  WHERE memid = memid AND status = 'Disputed';

END $$

DELIMITER ;

SET @PROC_CNT = 1;
call get_order_by_cust('test01', @CANCELED_CNT, @RESOLVED_CNT, @DISPUTED_CNT);
SELECT @CUR_COUNTER, @CANCELED_CNT, @RESOLVED_CNT, @DISPUTED_CNT;
```




### 계산

 *  difference in exact and approximate value handling

```SQL
drop procedure calAddDecimal;

DELIMITER $$
CREATE PROCEDURE calAddDecimal ()
BEGIN
  DECLARE i INT DEFAULT 0;
  DECLARE d DECIMAL(10,4) DEFAULT 0;
  DECLARE f FLOAT DEFAULT 0;
  WHILE i < 10000 DO
    SET d = d + .0001;
    SET f = f + .0001E0;
    SET i = i + 1;
  END WHILE;
  SELECT d, f;
END $$
DELIMITER ;


mysql> call calAddDecimal();
+--------+---------+
| d      | f       |
+--------+---------+
| 1.0000 | 1.00005 |
+--------+---------+
1 row in set (0.12 sec)
```

 * update decimal test

```SQL
create table decimal_test (
`seq` bigint(20) NOT NULL AUTO_INCREMENT,
`decimal_val` decimal(16,8) NOT NULL DEFAULT '0.00000000',
PRIMARY KEY (`seq`)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

mysql> insert decimal_test ( decimal_val) values (0);
```

```SQL
DELIMITER $$
CREATE PROCEDURE calAddDecimal2 ()
BEGIN
  DECLARE i INT DEFAULT 0;
  DECLARE d DECIMAL(10,4) DEFAULT 0;
  DECLARE f FLOAT DEFAULT 0;
  WHILE i < 10000 DO
    UPDATE decimal_test set decimal_val=decimal_val+0.0001 where seq=1;
    SET i = i + 1;
  END WHILE;
  SELECT `decimal` from decimal_test where seq=1;
END $$
DELIMITER ;

mysql> select * from decimal_test;
+-----+-------------+
| seq | decimal_val |
+-----+-------------+
|   1 |  1.00000000 |
+-----+-------------+
1 row in set (0.00 sec)
```


---
# Timezone

 * timezone 확인

```SQL
select @@global.time_zone, @@session.time_zone
```
 

 * 타임존 변경(재기동시에 원복됨)

```SQL
SET GLOBAL time_zone='Asia/Seoul';
SET time_zone='Asia/Seoul';
```


### 설정 방법

 * 시스템 시간대
 * 서버 시간대
 * 세션 시간대

### 데이터 타입

 * 시간대 정보를 고려하는 타입
   * timestamp

 * 그 외 데이터타입은 시간대 정보를 저장하지 않음

```SQL
select now(); <= 현재의 타임존 정보로 생성 정보를 문자열로 저장함.
```



---
# Function

 * 소수점 이하 스트링으로
   * SELECT SUBSTRING_INDEX(-0.1234567812345,'.',-1);

 * 소수점 이하 특정 자리수이상의 값을 확인(Java의 Round함수 FLOOR모드구현시 필요)
   * SELECT SUBSTRING(SUBSTRING_INDEX(-0.1234567812345,'.',-1), 9, char_length(SUBSTRING_INDEX(-0.1234567812345,'.',-1)));



# Backup and Restore

Take the dump from the remote RDS Server:

```
mysqldump -h rds.hostname -u remote_user_name -p remote_db > dump.sql
```

Upload it to your local MySQL instance:

```
mysql -u local_user_name -p local_db < dump.sql
```

 * option
   * -single-transaction
     * it dumps the consistent state of the database at the time when BEGIN was issued without blocking any applications

---
# Replication

### 상태 확인

```
mysql -u아이디 -p패스워드 -e 'SHOW SLAVE STATUS \G' | egrep "Master_Log_Pos|Running|IO_Err|SQL_Err"
mysql -u아이디 -p패스워드 -e 'SHOW SLAVE STATUS\G' | egrep "Master_Log_Pos|Running|Last|Seconds"
```

 * Slave_IO_Running와 Slave_SQL_Running이 모두 Yes,
 * Read_Master_Log_Pos와 Exec_Master_Log_Pos 값이 일치,
 * Last_Errno는 0, Last_Error는 공란, Seconds_Behind_Master이 0이면 정상 복제중



--- 
# 컬럼 검색

```SQL
SELECT * FROM INFORMATION_SCHEMA.COLUMNS A WHERE TABLE_SCHEMA = 'LESSONMON' WHERE A.COLUMN_NAME like '%SNS%';
```

---
# 용량

 * 테이블별 row 개수 확인(@Rollback(false) 로 테스트데이터 확인할 때 유용함.)

```SQL
SELECT table_name, table_rows FROM information_schema. tables WHERE table_schema = 'testdb' ORDER BY table_name;
```


---
# Backup

![](./BACKUP/README.md)
