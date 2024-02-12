---
layout: default
title: Overview
grand_parent: Container
parent: Docker
nav_order: 1
---

# 리소스들

 * 도커의 네트워크 관련 설정
   * https://docs.docker.com/network/network-tutorial-standalone/

 * container이름을 지정하지 않았을 때의 이름 생성 로직
   * https://github.com/moby/moby/blob/master/pkg/namesgenerator/names-generator.go

---
# Basic Command
* docker run
  * docker create + docker start


* 컨테이너 생성
  * docker create <image-name> <default command>


* default command
  * docker 실행중에 명령을 지정하면 명령은 기존의 설정을 override한다.
  * 기존에 생성된 container들은 변경이 불가능하다.
  * 아래는 busybox가 가지고 있는 default file system snapshot을 ls로 출력하는 내용.
```
$ docker run busybox ls
bin
dev
etc
home
proc
root
sys
tmp
usr
var
```


* docker create 나 docker run 은 컨테이너들을 매번 새로 만든다.


* docker logs <container id> 는 과거 로그도 나온다.


* docker stop과 docker kill의 차이
  * docker stop
    * SIGTERM 시그널을 보낸다.
  * 10초후에 SIGKILL을 보낸다.
  * docker kill
    * SIGKILL 시그널을 보낸다.



* 컨테이너는 다른 컨테이터의 파일시스템(하드디스크 segment)에 접근하지 못한다.
  * 2개의 다른 컨테이너에서 파일을 만들고, 다른 컨테이너에서는 보이지 않는 것으로 확인



* docker build 시에 이전에 만들어진 캐시를 그대로 사용한다.
* 하지만, RUN 명령의 순서가 바뀌거나 하는 경우에는 캐시를 사용하지 못하고, 새로운 이미지를 만든다.


* Taking Snapshot for running container
  * docker commit -c 'CMD ["redis-server"]' <container id>
    * -c default command를 지정
  * 생성 이미지를 [docker run b94ec51b41db] 명령을 실행하면, 컨테이너가 만들어지고 실행됨.

* Dockerfile의 WORKDIR 은 디렉토리가 없으면, 자동으로 만들어 준다.

 * basic commands

```
docker run --name mongo -d mongo
docker container run   -d -p 3306:3306 --name db_test01 -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql
docker container run -d --name webserver -p 8080:80 httpd
docker container run -d --name proxy -p 80:80 nginx

GENERATED ROOT PASSWORD: oa0eidaephiepoohaiG6iemaiqu9quel

docker container run -d --name nginx nginx
docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql

docker container top nginx
docker container inspect mysql
docker container stats
```

 * docker ps --no-trunc 요약안하는 옵션

 * interactive interface

```
docker container run -it --name proxy nginx bash
```


 * 이미 기동되있는 container에 접속하는 경우)

```
# docker container start mysql
# docker container exec -it mysql bash
root@f81fa44acf84:/#
```



# CMD vs ENTRYPOINT
### CMD
 * 컨테이너가 시작될 때 실행
 * Dockerfile에서 한번만 사용 가능
   * 마지막 CMD만 사용
 * CMD ["실행 파일", "매개 변수", "매개 변수 ..."]
 * container 에서 서비스 실행시에 사용
 
 ```
 CMD ["java", "-jar", "testprogram.jar"]
 ```
 * CMD는 cotainer실행시에 아래처림 지정하면 변경할 수 있다.
   + docker run [IMAGE] [COMMAND]에서 COMMAND를 넣으면 CMD가 무시
   + docker container run -it jdk-image-from-dockerfile /bin/bash

### ENTRYPOINT
 * CMD와 동일하게 컨테이너가 시작될 때 실행
 * CMD와 같이 있으면 ENTRYPOINT는 실행 파일, CMD는 매개변수 역할을 함
 * CMD와 유사하지만 ENTRYPOINT는 항상 실행되고, 변경되지 않는 부분이다.
 * docker run --entrypoint="[COMMAND] [IMAGE]"를 사용하여 무시 가능



### TEST 1

```
FROM debian:jessie-slim

CMD ["echo", "hello"]
CMD ["echo", "hello2"]
```

```
$ docker build -t basic . # build
```

```
$ docker run basic # run
hello2
```

```
$ docker run basic echo hello3 # run
hello3
```

### Test 2

```
FROM debian:jessie-slim

ENTRYPOINT ["echo"]
CMD ["hello"]
CMD ["hello2"]
```

```
$ docker build -t basic . # build
```

```
$ docker run basic
hello2
```

```
$ docker run --entrypoint="cat" basic /etc/hostname # run/docker
34n343hbfd
```

---
# ENV vs ARG

### ENV
 * build와 run 시점에 둘다 적용됨
 * 환경변수 지정
 * $변수 혹은 ${변수} 형태로 표현 가능
 * 또한, ${변수:-값}으로 값을 기본값으로 표현 가능
 * ${변수:+값}의 경우는 반대에 경우인데 사용할 일이 있을까 싶다.
 * docker run 시에 --e 옵션을 활용하여 **오버라이딩** 할 수 있다.

### ARG
 * build 시점에만 사용되는 변수
 * ARG 변수 혹은 ARG 변수=값 형태로 표현 가능
 * ENV처럼 ${변수:+값}, ${변수:-값}으로도 표현 가능
 * docker build 시에 --build-arg 옵션을 활용하여 **오버라이딩** 할 수 있다.

### Test 1 (Scope 확인)
```
FROM debian:jessie-slim

ENV NAME_ENV=wonchul
ARG NAME_ARG=wonchul

RUN echo "envirment = ${NAME_ENV:-WonChul}" \
    && echo "argument = ${NAME_ARG:-WonChul}"

CMD echo "envirment = ${NAME_ENV:-WonChul}" \
    && echo "argument = ${NAME_ARG:-WonChul}"
```

1. override 안했을 때(build, run)

```
$ docker build -t basic . # build
# 결과
...
envirment = wonchul
argument = wonchul
...
```

```
$ docker run basic # run
# 결과
envirment = wonchul
argument = WonChul
```

2. override 했을 때(build, run)

```
$ docker build -t basic --build-arg NAME_ARG=WONCHUL . # build
# 결과
...
envirment = wonchul
argument = WONCHUL
...
```

```
$ docker run -e NAME_ENV=WONCHUL basic # run
# 결과
envirment = WONCHUL
argument = WonChul
```

### Test 2 (ENV와 ARG 덮어쓰기)
```
FROM debian:jessie-slim

ARG NAME
ENV NAME=${NAME:-WonChul}

RUN echo "NAME = ${NAME}"

CMD echo "NAME = ${NAME}"
```

1. override 안했을 때(build, run)
```
$ docker build -t basic . # build
# 결과
...
NAME = WonChul
...
```

```
$ docker run basic # run
# 결과
NAME = WonChul
```

2. override 했을 때(run)
```
$ docker run -e NAME=WONCHUL basic # run
# 결과
NAME = WONCHUL
```

3. override 했을 때(build, run)
```
$ docker build -t basic --build-arg NAME=WONCHUL . # build
# 결과
...
NAME = WONCHUL
...
```

```
$ docker run basic # run
# 결과
NAME = WONCHUL
```

```
$ docker run -e NAME=WONCHUL_ENV basic # run
# 결과
NAME = WONCHUL_ENV
```

---
# ADD vs COPY
> ADD had so many functionalities proved to be problematic in practice, as it behaved extremely unpredictable. The result of such unreliable performance often came down to copying when you wanted to extract and extracting when you wanted to copy.

호환성 이슈 때문에 바꾸지는 못하고, COPY 명령을 추가하였다.

### ADD
 * 파일 복사
 * 압축 파일인 경우, 압축을 품
 * 단, URL로 가져온 파일은 압축만 해제하고 풀지는 않음
 * OS에 따라서 압축 해제 여부가 있음
 * 파일은 소유 root:root과 기존 권한을 가짐
 * URL은 소유 root:root과 600 권한을 가짐
 * COPY 명령이 더 단순하기 때문에, COPY사용이 추천됨

### COPY
 * 파일 복사
 * ADD와 달리 파일 그대로 가져옴
 * 권한 그대로 설정
 * COPY file <file system inside container>
   * COPY test-program.jar /usr/local/bin/
 * 파일 경로 설정시에는 docker를 기동시켜서 확인하면서 작업하는 것을 추천
   * docker container run -it jdk-image-from-dockerfile

### Which to Use (Best Practices)
Docker released an official document outlining best practices for writing Dockerfiles, which explicitly advises against using the ADD command.

Docker’s official documentation notes that COPY should always be the go-to instruction as it is more transparent than ADD.

If you need to copy from the local build context into a container, stick to using COPY.

The Docker team also strongly discourages using ADD to download and copy a package from a URL. Instead, it’s safer and more efficient to use wget or curl within a RUN command. By doing so, you avoid creating an additional image layer and save space.

Let’s say you want to download a compressed package from a URL, extract the content, and clean up the archive.

Instead of using ADD and running the following command:

```
ADD http://source.file/package.file.tar.gz /temp
RUN tar -xjf /temp/package.file.tar.gz \
 && make -C /tmp/package.file \
 && rm /tmp/ package.file.tar.gz
```

You should use:
```
RUN curl http://source.file/package.file.tar.gz \
 | tar -xjC /tmp/ package.file.tar.gz \
 && make -C /tmp/ package.file.tar.gz
```


### 디렉토리 복사시 예
```cmd
ADD go /usr/local/
```
will copy the contents of your local go directory in the /usr/local/ directory of your docker image.

To copy the go directory itself in /usr/local/ use:

```cmd
ADD go /usr/local/go
or
COPY go /usr/local/go
```



---
# WORKDIR
- 도커파일 뒤에 오는 모든 지시자(RUN, CMD, COPY, ADD 등)에 대한 작업 디렉토리를 설정
- 리눅스 명령어의 cd와 비슷한 역할
- 작업 디렉토리를 별도로 지정하여, 로컬에 있는 파일을 도커 컨테이너로 복사할 때 분리하는데 쓰임


---
# MAINTAINER vs LABEL

* MAINTAINER보다 사용용도가 넓은 LABEL을 사용하는 것이 추천됨
```
MAINTAINER <name>
LABEL maintainer="someone@gmail.com" <= LABEL로 변경한 경우
LABEL creationdate="19 November 2019"
```

* 사용예
  * https://hub.docker.com/r/tmaier/docker-compose/dockerfile


# Repository에 이미지 PUSH

* image tag로 이미지에 push하기 위한 명칭을 할당후 push하게 된다.
  * docker login 명령으로 docker hub에 로그인할 수 있다.
```
docker image tag {로컬 이미지이름} {아이디}/{이미지이름}
docker push {아이디}/{이미지이름}
```
---
# Network

* network 정보 조회
  * docker network ls

* network 생성
  * docker network create my-network

```
# docker container run -p
```

 * 포트 체크
   * docker container port <container>
```
# docker container run -p 80:80 --name webhost -d nginx
e436fc046b787ab60b78c1c1126301d0261474aac7396701d20712d4d60d7bd4
# docker container port webhost
80/tcp -> 0.0.0.0:80
```

 * inspect --format
```
# docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
172.17.0.3
```

* Create docker network and configure
```
# docker network ls

# docker network inspect bridge

# docker network create my_app_net

# docker container run -d --name new_nginx --network my_app_net nginx    <-- 생성한 가상 네트워크를 사용하도록 컨테이너를 실행

# docker network connect fd7e3f59b264 e436fc046b78 <-- 다른 컨테이너도 my_app_net에 연결 시킬 수 있다.

# docker network inspect my_app_net   <- 생성한 가상 네트워크의 상세 정보 확인(이제 2개 컨테이너가 할당된 것을 알 수 있다.)

# docker network disconnect fd7e3f59b264 e436fc046b78 <- 다시 연결을 해제시킬 수 있다.


# docker container run -d --name my_nginx --network my_app_net nginx
902df199e9ec290290ada9ace0d7ce72464829ac6a92523c3d160226f6a5008d

# docker container exec -it my_nginx ping new_nginx

# docker container create --link ...
```

### EXPOSE

* EXPOSE 명령으로 포트를 열었어도 docker container run -p port:port -p옵션으로 포트를 지정해줘야 한다.


---
# Volume ( Persistent Data )

 Always host wins.

 * 호스트머신에서 파일을 수정하면
   * We edit files with editor on our host using native tools
   * Container detects changes with host files and updates web server
   * start container with docker run -p 80:4000  -v $(pwd):/site bretfisher/jekyll-serve
   * refresh our browser to see changes
   * Change the file in _post\ and refresh browser to see changes

  * Bind Mount
    * Can't us in Dockerfile, must be at [cotainer run]
      + docker-compose.yml 파일에서는 설정 가능한 듯
	  * ... run -v /Users/eddy/stuff:/path/container
    * -v A:B 옵션으로 지정하면 호스트의 A디렉토리와 컨테이너의 B 디렉토리를 공유한다는 의미



 * Command about docker volmue
   * docker volume ls
   * docker volume inspect ce1d4a...

 * Can use inspect many way
   * docker image inspect mysql
   * docker container inspect mysql

 * Volume is not deleted automatically
 * start MySQL container
   * docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql
   * named volume
     * docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql  

 * You can create Volume alone.
   * docker volume --help

 * Tip
   * cd dockerfile-sample-2
   * temporarly run
     * docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx
   * without -v you can see default nginx page
     * docker container run -d --name nginx2 -p 8080:80  nginx



---
# docker container 명령

* daemoninze
  * docker run -d

* Automatically remove the container when it exits
  * docker container run --rm -it centos:7 bash
  * docker container run --rm -it ubuntu:13.04 bash


* DNS Round Robin Test
```
# docker network create dude
# docker container run -d --net dude --net-alias search elasticsearch:2
# docker container run -d --net dude --net-alias search elasticsearch:2
# docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
6a26debcfcb7        elasticsearch:2     "/docker-entrypoint.…"   11 seconds ago      Up 9 seconds        9200/tcp, 9300/tcp                  nervous_hugle
bcf3fb423c0e        elasticsearch:2     "/docker-entrypoint.…"   3 minutes ago       Up 2 minutes        9200/tcp, 9300/tcp                  thirsty_jackson

# docker container run --rm --net dude alpine nslookup search

Name:      search
Address 1: 172.19.0.2 search.dude
Address 2: 172.19.0.3 search.dude

# docker container run --rm --net dude centos curl -s search:9200
{
 "name" : "Scorpio",
 "cluster_name" : "elasticsearch",
 "cluster_uuid" : "ku0HF7bGTpmJ3jmbFLOZIQ",
 "version" : {
   "number" : "2.4.6",
   "build_hash" : "5376dca9f70f3abef96a77f4bb22720ace8240fd",
   "build_timestamp" : "2017-07-18T12:17:44Z",
   "build_snapshot" : false,
   "lucene_version" : "5.5.4"
 },
 "tagline" : "You Know, for Search"
}
```

* 생성되있는 container를 실행하고 로그를 확인하는 예
```
docker container start ae
docker container logs -f ae
```

### docker container commit

 * container에서 image를 생성하는 예
 * Dockerfile을 이용해서 image를 생성하는 것이 일반적인 방법이다.
   * Dockerfile이 있는 경로에서 docker image build -t {이미지명}

 * image에 접속해서, jdk를 설치하는 예.
```
docker container run -it ubuntu
apt-cache search jdk
apt-get install -y openjdk-8-jdk
exit

docker container commit -a "created by july" 5f myjdkimage <= 위에서 jdk를 설치한 컨테이너를 베이스로 이미지 생성
docker image ls  <= 여기서 생성한 이미지를 확인 가능함
docker container prune
docker run -it myjdkimage <= JDK가 설치되어있는 것을 확인 가능하다.
```

---
# 비정상 종료 관련 처리

### 테스트 케이스

 * crash.sh
```
#/bin/bash
sleep 30
exit 1
```

 * Dockerfile
```
FROM ubuntu:14.04
ADD crash.sh /
CMD /bin/bash /crash.sh
```

 * create container
```
sudo docker build -t testing_restarts ./
```

 * image by executing
```
sudo docker run -d --name testing_restarts testing_restarts
```

 * --restart no
   * 기본 동작으로 재기동 안함

 * --restart always
   * 프로그램이 죽는 경우나, 리부팅할 때도 기동됨
   * 매번 재기동 되는 것이 싫다면, unless-stopped 으로
```
sudo docker run -d --name testing_restarts --restart always testing_restarts
```

 * --restart unless-stopped
   * behaves the same as always with one exception.
   * When a container is stopped and the server is rebooted or the Docker service is restarted, the container will not be restarted.
   * always하고 유사하지만, 컨테이너를 종료시키지 않은 경우에만 restart한다



 * --restart on-failure
   * Restarting on failure but stopping on success
   * 리부팅할 때도 최종 반환값이 0이 아닌 경우에는 기동됨.
```
sudo docker run -d --name testing_restarts --restart on-failure testing_restarts
혹은 아래처럼 횟수를 지정할 수 있다. 지정하지 않으면 횟수는 무제한이다.
sudo docker run -d --name testing_restarts --restart on-failure:5 testing_restarts
```

 * 이미 생성된 docker의 restart policy변경
   * docker update --restart=always <container>


---
# TIPS

* Container 하나에 하나의 서비스만 운영하는 것이 바람직하다.
* 여러 서비스를 같이 운영하면, 서비스가 healthy인지 여부를 확인하기 어렵다.
* 여러가지 상황에서 관리하기도 좋다.

### rebuild
 * 파일 시스템의 스냅샷을 base로 동작하기 때문에, 소스만 수정해서는 적용되지 않는다.
   * 이미지 빌스시 수정한 시점 이후의 스냅샷을 전부 다시 빌드해야 한다.
   * 수정이 자주 되는 파일(ex 소스)는 Docker파일에서 뒤로 미루는게 좋다.

### Directory 이동

* WORKDIR 작업 디렉토리 지정
* docker container run -it 명령으로 실행시에도 WORKDIR로 이동된다.

### 디스크 사용량 관련

 * docker image prune
   * clean up just "dangling" images

 * docker system prune
   * will clean up everything

 * docker image prune -a
   * will remove all images you're not using

 * docker system df
   * see space usage.

 * docker container prune
   * start상태가 아닌 container를 삭제


### Timezone 변경
```
docker run --name felis-mysql-5.7.29 -p 3307:3306 -v /etc/localtime:/etc/localtime:ro -e TZ=Asia/Seoul -v /home/sdev/docker_volumes/mysql-5.7:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=manager -d mysql:5.7.29

(타임존 변경)
sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime

docker run
-v /etc/localtime:/etc/localtime:ro
-e TZ=Asia/Seoul \
```

### 이미지에서 dockerfile을 만드는 방법

 https://stackoverflow.com/questions/19104847/how-to-generate-a-dockerfile-from-an-image

  * docker history
 ```
 docker history --no-trunc $argv  | tac | tr -s ' ' | cut -d " " -f 5- | sed 's,^/bin/sh -c #(nop) ,,g' | sed 's,^/bin/sh -c,RUN,g' | sed 's, && ,\n  & ,g' | sed 's,\s*[0-9]*[\.]*[0-9]*\s*[kMG]*B\s*$,,g' | head -n -1

 tac : reverse the file
tr -s ' '                                       trim multiple whitespaces into 1
cut -d " " -f 5-                                remove the first fields (until X months/years ago)
sed 's,^/bin/sh -c #(nop) ,,g'                  remove /bin/sh calls for ENV,LABEL...
sed 's,^/bin/sh -c,RUN,g'                       remove /bin/sh calls for RUN
sed 's, && ,\n  & ,g'                           pretty print multi command lines following Docker best practices
sed 's,\s*[0-9]*[\.]*[0-9]*\s*[kMG]*B\s*$,,g'      remove layer size information
head -n -1                                      remove last line ("SIZE COMMENT" in this case)

 ```

  * chenzj/dfimage 이용하는 방법
 ```
 docker pull chenzj/dfimage
alias dfimage="docker run -v /var/run/docker.sock:/var/run/docker.sock --rm chenzj/dfimage"
dfimage IMAGE_ID > Dockerfile
 ```


---
# TroubleShooting

### 리다이렉션을 사용하는 경우
If you are going to do pipe redirections in your command, pass it as a string to /bin/sh:

```
docker exec <container_id> /bin/sh -c 'mysql -u root -ppassword </dummy.sql'
```
