---
layout: default
title: Overview
grand_parent: AWS
parent: ECS
nav_order: 1
---


# Elastic Container Service Overview
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}


* https://www.github.com/awslabs/data-on-eks


# ECS
ECS는 간편하지만, 기능에 제약이 많다. 왜냐면, ECS는 AWS에서만 쓰는 기능이라서 그러지 않을까한다.

AWS 엔지니어조차 마이크로서비스와 같이 복잡한 케이스에서는 쿠버네티스를 추천한다.

웨비나에서 듣기로는 cluster를 매니지먼트 하기 위한 어려운 사용법때문에 kubernetes대신에 ECS를 사용하는 예가 많이 있었다고 한다.

리소스 매니징을 자유롭게 하고자 하는 경우는 fargate보다 EC2기반의 ECS를 사용하는게 좋다.

---
# Use Case

 * Rebuilding Our Infrastructure with Docker, ECS, and Terraform
   * https://segment.com/blog/rebuilding-our-infrastructure/
   * => 멀티 어카운트로 설정하는 예.
   * => local dns + ELB 조합으로 서비스 디스커버리를 대체했다.

 * Best Practices - Running your application with Amazon ECS
   * https://docs.aws.amazon.com/AmazonECS/latest/bestpracticesguide/application.html
   * ECS 이용시의 여러가지 가이드라인이 정리되어있다. 꽤나 유용한 듯!

---
# ECS

 ![](/images/aws/ecs/ecs-1.png)
 ![](/images/aws/ecs/ecs-2.png)
 ![](/images/aws/ecs/ecs-3.png)
 ![](/images/aws/ecs/ecs-4.png)
 ![](/images/aws/ecs/ecs-5.png)
 ![](/images/aws/ecs/ecs-6.png)
 ![](/images/aws/ecs/ecs-7.png)
 ![](/images/aws/ecs/ecs-8.png)
 ![](/images/aws/ecs/ecs-9.png)
 ![](/images/aws/ecs/ecs-10.png)
 ![](/images/aws/ecs/ecs-11.png)
 ![](/images/aws/ecs/ecs-12.png)
 ![](/images/aws/ecs/ecs-13.png)
 ![](/images/aws/ecs/ecs-14.png)
 ![](/images/aws/ecs/ecs-15.png)
 ![](/images/aws/ecs/ecs-16.png)
 ![](/images/aws/ecs/ecs-17.png)
 ![](/images/aws/ecs/ecs-18.png)
 ![](/images/aws/ecs/ecs-19.png)
 ![](/images/aws/ecs/ecs-20.png)
