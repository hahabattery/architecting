---
layout: default
title: Linux
grand_parent: Tools
parent: OS
nav_order: 4
---

 * unix timestamp구하기
```
date +%s
```

 * timestamp to date
```
date -d@1568082327
```

# 성능 관련

### top 명령


 * 스레드별로 모니터링
```
top -H
```

 * ordering
```
F -> s  (top명령 실행상태에서)
```

* k - kill process

##### Sorting the process list
* ‘M’ to sort by memory usage
* ‘P’ to sort by CPU usage
* ‘N’ to sort by process ID
* ‘T’ to sort by the running time
* ‘R’ to sort by 오름차순과 내림차순을 토글 변경합니다.


### ps

 * 스레드별로 조회
 ```
 ps -efL
 ```

 * 기동시간 확인
 ```
 ps -eo pid,lstart,cmd
 ```

# port 관련

 * dynamic port range구하기
   * cat /proc/sys/net/ipv4/ip_local_port_range


 * listen port 검색
   * sudo netstat -nap |grep -E '356([0][0-9]|[1]?[1][0-5])'


# 파일 복사
```
rsync -av ./mysql_dump_util btrade@172.31.36.100:/service/backup/
rsync -rvp  <= 파일 속성 유지하면서 recursive하게 복사하는 예
```

 * rsync 숨김파일까지 복사하는 옵션
```
rsync -avrpEz ./ wcsong@192.168.0.15:/home/wcsong/from_11/
```

# 파일 검색

 * find command
   * -cmin create
   * -amin access
   * -mmin modify

 * 파일 이름으로 검색
```
find ./ -name nohup.out -exec ls -lh {} \;
```


 * 10M이상 파일 검색
```
sudo find / -type f -size +10M -exec ls -lh {} \;
```

 * find files accessed more than 25 but less than 35 minutes ago.
   * find -amin +25 -amin -35

 * find /etc -mmin 5 5분내에 수정된 파일 리스트

# 파일 편집

### 특수키

 * vi ^M수정
   * $ vim
   * :set ffs=unix

 * vi ^M 입력방법
   * Ctrl + v, m

# 파일 내용 변경

 * 10.2를 10.3으로 변경하는 예
sudo sed -i 's/10.2/10.3/' /etc/apt/sources.list.d/MariaDB.list


# 패키지 관리

### 우분투 데비안 계열


 * apt list --installed {패키지명}
   * 설치된 패키지 조회

 * dpkg-query -L <package_name>
   * To see all the files the package installed onto your system
   * 패키지 설치하면서 같이 생성된 모든 파일 리스트를 조회
   * grep으로 조회하면 쉽게 찾을 수 있다

 * dpkg -S 파일을 소유하고 있는 패키지를 조회


### CentOS
 * yum list 명령어로 java리스트를 확인
   * yum list java*jdk-devel
   * list 확인후에는 yum install java-1.7.0-openjdk-devel.x86_64 처럼 버전을 지정해서 설치 가능
 * 설치 패치지 확인
   * rpm -qa java*jdk-devel

 * 리포지토리 추가하기 키워드
   + "add yum repo amazon linux"
   + "add yum repo redhat linux 9"


# Debugging

디버깅의 시작은 과감히 툴을 먼저 배우는 것이라고 해도 과언이 아닐정도로 툴에
 익숙해야합니다. 디버깅 툴은 어떻게 보면, 크게 구분이 되어 있는 것은 아니지만
 사뭇 심도있는 분석을 위해서는 해킹에 사용되는 것과 크게 다르지 않습니다.
디버깅과 해킹은 같은 맥락에 있는 것이지요.

먼저 쉽고 재밌게 접근할 수 있는 것이 system call tracer입니다.

linux: strace
 solaris: truss
 hpux: trace(10.x), tusc(11.x)

로 알려져 있는 것들이지요. 위 프로그램들의 option 들이 대개 비슷합니다.

strace ls

만 해도 나오는 내용이 어떻다는 것을 보실 수 있을텐데요.
이들은 모두 kernel level의 함수들입니다. 즉 system call이라는 것이지요.

직관적으로 이용할 수 있는 방법은 다음과 같은 것들입니다.

1. 어떤 shared library가 사용되는지 알수 있음.
2. 1번과 비슷하지만 어떤 파일을 열다가 실패하는지,
대개 configuration file을 global, home.. 순으로 찾지요.
3. process가 잠시 멈출때, 어떤것을 대기하고 있는지.
4. 전송되고 들어오는 내용은 무엇인지 (-s 1024 option)
 5. 어떤 signal을 받는지.
6. ipc 객체들은 어떤것들이 이용되는지.

등등...

system call은 기본적으로 OS를 다루는 방법에 대한 것이므로, 많은 hint를 얻을 수
 있습니다.

option 들중에 중요한 것 몇가지만 소개하자면, system call의 가장 대표적인 것중의
 하나는 실행되고 있는 daemon의 현재 작업내용을 살펴볼수 있는 것이 있습니다.

strace -p <pid>

형태로 실행중인 process를 살펴보는 것이지요. 더불어 daemon의 경우 fork가
 일어나는 경우가 많은데,

strace -f -p <pid>

-f option을 주어 fork되어 나오는 process까지 trace 하라는 것입니다. fork외에
vfork도 추적할 수 있어야하므로 대개 f를 쓸때는 다음과 같이 사용합니다.

strace -fF -p <pid>

더 줄여서

strace -fFp <pid>

로 사용하지요.

이것들을 종합하여 다음과 같은 용도로 사용할 수 있습니다.

1. daemon 이 갑자기 멈추었는데, debug 용 printf를 집어 넣지 않았을 때, 알고싶은
 경우.
2. socket server가 과연 process가 connection을 접수한 뒤 제대로 fork 되는지.
3. telnet 서버에 접속하였는데, prompt가 떨어지지 않는 경우 inetd가 무슨일을
 하는지. (대개 tcp_wrapper에 의해 DNS IP resolve 하는 경우가 많죠.)

지금까지 한 것은 일부분에 지나지 않습니다. 남의 program을 추적할때 system call
 trace를 하는 것만으로 process가 하는 일을 상상할 수 있다면, 프로그램을
 만든사람은 어느 code를 지나가고 있는지 알 수 있을 것입니다.
