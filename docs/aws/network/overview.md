---
layout: default
title: Overview
description: 
nav_order: 5
parent: Network
---


# Network
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}


### ENI
탄력적 네트워크 인터페이스

관련해서 사용할 리소스와 같은 AZ에 있어야 ENI를 사용할 수 있다.

ENI에는 보안그룹을 설정할 수 있다.

ENI는 별도 요금이 부과되지는 않는다.



### VPCE
* VPC내부의 리소스들은 Internet Gatway, NAT Gatway, VPC Endpoint 를 통하지 않으면 S3와 같은 global이나 regional 리소스에 접근할 수 없다.
* internet gateway나 Nat gateway는 S3같은 리소스 접속시에 외부 인터넷에 공개적으로 연결되어서 트래픽이 노출되게 된다.
* S3는 global 리소스인데 Internet Gateway를 이용하지 않고 VPC gateway endpoint (VPCE)를 만들고나서 private link를 이용해서 안전한 연결이 가능하다.
* VPC Endpoint는 VPC 내부 리소스가 VPC 외부 AWS서비스와 통신할 때, public ip를 사용하지 않고 S3나 Kinesis와 같은 AWS 서비스에 접근할 수 있도록 해준다.

VPC endpoint는 interface endpoint와 gateway endpoint로 나뉜다.
##### interface endpoint
![](/images/aws/VPCE-interface-endpoint.png)
interface endpoint는 1개 이상의 Subnet에 Endpoint Network Interface(ENI)를 생성한뒤에 사설 IP를 부여한다.

ENI를 이용해서 외부 서비스에 접근이 가능하다.

Private DNS names enabled를 활성화 하지 않으면, 공인 IP주소를 불러오므로 주의할 것



##### gateway endpoint
![](/images/aws/VPCE-gateway-endpoint.png)
gateway endpoint는 interface endpoint와는 달리 라우팅테이블에 라우팅만 추가한다. 자동으로 추가되며 변경할 수 없다.

S3와 DynamoDB 두개 서비스만 사용 가능.


# Direct Connect
* [Network-to-Amazon VPC connectivity options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html)


* 직접 적용했었던 Direct Connect 설정 예
  + EC2에 ENI를 추가해서 특정서브넷에 인터페이스를 세팅하고, 해당 서브넷에 라우팅 테이블을 추가한다.
  + 이 라우팅 테이블에는 VPG(Virtual Private Gateway)에서 상대편 네트워크 정보(Topology)가 전파된다. 


