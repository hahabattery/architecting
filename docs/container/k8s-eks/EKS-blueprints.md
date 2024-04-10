---
layout: default
title: EKS Blueprints
nav_order: 3
parent: EKS
grand_parent: Container
---



IDP로 EKS를 이용하는 내용의 웨비나(20231029_복잡한_내부_개바플랫폼(IDP)_구축_Amazon_EKS_Blueprint로_시작하기.pdf)가 있어서 정리해봤다.

# IDP (Internal Developer Platform)
내부 개발 플랫폼이라는 용어이며, 사내에 여러 다른 서비스들에 통일된 인프라스트럭쳐의 이용 규칙을 적용하는 것을 의미한다.

* 표준화를 통해서 운영 업무를 줄이고
* 셀프 서비스를 통해 리드 타임을 줄이고
* 안전한 릴리스로 배포 주기를 줄일 수 있다.

### Amazon EKS Blueprints
EKS의 생태계가 너무 거대하기 때문에, 사용자가 EKS의 구축을 도와주는 청사진이다.

특히 EKS Blueprints Add-on 이라는 게 있어서 손쉽게 필요한 구성을 추가할 수 있다.

# 리소스
CDK 버전과 Terraform 버전이 각기 따로 있으니, 사용하는 툴에 따라서 확인하면 된다.

### CDK EKS Blueprints
* https://aws-quickstart.github.io/cdk-eks-blueprints
* https://github.com/aws-samples/cdk-eks-blueprints-patterns/tree/main/lib


### Terraform EKS Blueprints
* https://aws-ia.github.io/terraform-aws-eks-blueprints/
* https://github.com/aws-ia/terraform-aws-eks-blueprints/tree/main/patterns
