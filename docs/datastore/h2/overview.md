---
layout: default
title: Overview
nav_order: 1
grand_parent: Datastore
parent: H2
---

[H2 SQL 문법](https://www.h2database.com/html/grammar.html)


# H2 날짜 함수 문법, MySQL 비교 (DAYOFWEEK, DATE_FORMAT, DATE_ADD)
```sql
-- 날짜로 숫자 형태 요일 구하기 일요일 1 ~ 토요일 7
-- MySQL
SELECT DAYOFWEEK('2021-07-24') FROM DUAL;

-- H2
SELECT DAY_OF_WEEK('2021-07-24') FROM DUAL;


-- 날짜 형식 변환
-- MySQL
SELECT DATE_FORMAT(NOW(),'%Y-%m-%d') FROM DUAL;

-- H2
SELECT FORMATDATETIME('2021-07-24 13:33:42', 'yyyy-MM-dd') FROM DUAL;

-- 날짜 더하기
-- MySQL
SELECT DATE_ADD(NOW(), INTERVAL 1 DAY) FROM DUAL;

-- H2
SELECT TIMESTAMPADD(DAY, 1, NOW()) FROM DUAL;

-- 문자열을 날짜 형식으로 변환
-- MySQL
SELECT STR_TO_DATE('2000-01-31', '%Y-%m-%d') FROM DUAL;

-- H2
SELECT PARSEDATETIME ('2000-01-31', 'yyyy-MM-dd') FROM DUAL;
```



# H2 setup (tcp)

Download h2 package from https://www.h2database.com

Typing ./bin/h2.sh will run h2 console .

If there is no permission, grant permission with chmod 755 ./bin/h2.sh

Connect to the h2 console, change only the JDBC Url part to jdbc:h2:~/my-db-test format, and click the "Connect" button.

After disconnecting from h2 console, change to jdbc:h2:tcp://localhost/~/my-db-test format and connect.

### 기본 포트
 * web 8082
 * tcp는 5435


# 포트 변경
~\h2.bat 을 실행할 때

뒤에 인자로
 * webPort 변경할 포트
 * tcpPort 변경할 포트

```command
h2.bat -webPort 8088 -tcpPort 9099
```

# embedded 된 경우

### SpringBoot에 embedded된 경우

아래처럼 설정되면 h2가 embedded되어서 동작한다.
```YAML
spring:
  h2:
    console:
      enabled: true
  datasource:
    hikari:
      driver-class-name: org.h2.Driver
      username: sa
      password:
      jdbc-url: jdbc:h2:mem:test
      connection-test-query: SELECT 1
```

아래 형식대로 URL을 입력하면 H2 콘솔에 접속할 수 있다.
```
http://localhost:8080/h2-console
```


