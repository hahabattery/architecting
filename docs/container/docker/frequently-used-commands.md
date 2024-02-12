---
layout: default
title: Frequently Used Commands
grand_parent: Container
parent: Docker
nav_order: 4
---


# MySQL

### test-local-mysql

 * 로컬에서 MySQL을 설정해서 테스트하는 설정
 * 아래와 같이 로컬에 MySQLDB 를 도커로 기동시켜서 사용할 것.

```
1) 윈도우즈의 경우 D드라이브의 dockerdata/mysql57/ 폴더를 생성한다.

2) docker run -d -p 23306:3306 -e MYSQL_ROOT_PASSWORD=test --name bulit-test-mysql -v D:/dockerdata/mysql57/data:/var/lib/mysql mysql:5.7.37 --character-set-server=utf8mb4 --collation-server=utf8mb4_general_ci

3) create database testdb DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

 * 타임존 설정은 하고 있지 않은데, 디폴트로 System 설정을 따라가게 되어있음. 


 * 테이블별 row 개수 확인(@Rollback(false) 로 테스트데이터 확인할 때 유용함.)
```
SELECT table_name, table_rows FROM information_schema. tables WHERE table_schema = 'testdb' ORDER BY table_name;

```


##### docker: no matching manifest for linux/arm64/v8 in the manifest list entries.
 m1 mac에서 나올 수 있는 에러메세지이다.
 https://docs.docker.com/desktop/mac/apple-silicon/

> Not all images are available for ARM64 architecture. You can add --platform linux/amd64 to run an Intel image under emulation. In particular, the mysql image is not available for ARM64. You can work around this issue by using a mariadb image.
 
 * 위 정보에서 알 수 있듯이 mysql이미지는 Arm 아키텍처를 이용하는 mac m1 을 지원하지 않기 때문에, mac m1에서는 테스트시에 주의가 필요하다. 
 * 테스트 용도로 mysql을 m1 mac에서 intel이미지를 에뮬레이션으로 도커로 이용하는 경우에는 docker 명령에 --platform linux/amd64를 지정해서 실행하면 된다.
```shell
docker run -d --platform linux/amd64 -p 23306:3306 -e MYSQL_ROOT_PASSWORD=test  --name bulit-test-mysql -v /Users/wonchangsong/Documents/dockervolumes/mysql5.7.37.20220430 mysql:5.7.37 --character-set-server=utf8mb4 --collation-server=utf8mb4_general_ci

create database testdb; 
```

### 타임존 설정

환경변수로 설정하는 방법이 현재로썬 존재하지 않기 때문에, 아래처럼 Docker파일을 생성하는 방법을 이용해야 한다.
참고) 아래 샘플에는 볼륨 처리가 없음. 설정할 것
```
mysqldb:
    image: mysql:5.7.37
    build: .
    container_name: mysql_container
    ports:
      - "23306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=test
      - TZ=Asia/Seoul
```

### 테스트를 위해서 타임존을 설정하는 경우
 * 설정 확인
```
select @@global.time_zone, @@session.time_zone;
```

 * 타임존 변경
```
SET GLOBAL time_zone='Asia/Seoul';
SET time_zone='Asia/Seoul';
```




