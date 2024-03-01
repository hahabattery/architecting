---
layout: default
title: Trouble Shooting
description: AWS를 이용하면서 발생한 장애 상황들에 대한 대응 이력
nav_order: 5
parent: AWS
---


# Trouble Shooting
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}




# EBS 가 문제가 있는 경우의 대응 예

* EC2를 reboot 해보고
* EC2를 shutdown -> start 해보고
* 메가존 엔지니어가 인스턴스를 새로 띄워서 EBS를 붙여보고(root 암호 있이)
* 메가존 엔지니어가 인스턴스를 새로 띄워서 EBS를 붙여서(root암호 없이 <= 엔지니어는 이런 설정이 가능한 듯?)
* ssh로 시리얼 접속
  + 가이드) https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-to-serial-console.html (edited)

젠킨스를 설치해서 사용하던 인스턴스가 문제였었는데, 백업도 없어서 결국에는 새로 설치해서 설정을 다시 했다.
