---
layout: default
title: EC2 or Fargate
grand_parent: AWS
parent: ECS
nav_order: 1
---


# EC2 or Fargate
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}


ECS EC2와 Fargate가 설정방식이 조금씩 달라서, 중요 포인트를 정리한다.

# 다이나믹 오토스케일링
EC2 리소스에 대한 설정이다.

### CapacityProviderReservation
컨테이너를 동작시키기 위한 EC2리소스들의 예비 용량을 설정한다.

100이 디폴트 값이다.


### targetCapacity
컨테이너 동작에 사용되는 가용성 목표를 설정한다. 

25로 설정하면, 인스턴스들의 25%가 Task에 사용되고 나머지 75%가 예비 용량으로 활용됨.


N = 기존에 존재하는 인스턴스 개수

M = Tasks가 수행하는데 필요한 전체 인스턴스 개수

targetCapacity percent = M / N * 100

targetCapacity를 25로 설정하면 N이 M의 4배(N = 4 X M), 인스턴스들의 25%가 Task에 사용되고 나머지 75%가 예비 용량으로 활용됨.

* **참고 - 300초의 warm up 기간이 적용됨.**

둘중에 머가 우선이지?


### ECS 태스크의 Scale In / Scale Out 조건
태스크에 대한 설정이다.

아래와 같은 형태로 설정한다.
```
Scale In 조건 : 60초동안 지속적으로 CPU 30%이하
Scale Out 조건: 60초동안 지속적으로 CPU 30%이상
```

* **참고 - 인스턴스가 기동되기까지의 120초의 warm up 기간이 적용됨.**


### 사용 시나리오
 * EC2설정에서 인스턴스 개수를 설정할 수 있다.
   + 최소(3) 최대(5) 라고 했을 떄,

 * EC2에 대한 설정내용은 다음처럼 적용될 것이다.
   + CapacityProviderReservation 로 100
   + N = 기존에 존재하는 인스턴스 개수
   + M = Tasks가 수행하는데 필요한 전체 인스턴스 개수 
   + targetCapacity percent = M / N * 100
   + targetCapacity를 25로 설정하면 N이 M의 4배(N = 4 X M), 인스턴스들의 25%가 Task에 사용되고 나머지 75%가 예비 용량으로 활용됨.
   + 참고) 300초의 warm up 기간이 적용됨.

 * ECS 태스크의 오토스케일링 설정을 다음과 같이 할 수 있다.
   + 태스크의 종류를 보고 어떤 리소스(CPU/Memory/리퀘스트수/트래픽양)을 조건으로 오토스케일링 정책을 작성하게 된다.


