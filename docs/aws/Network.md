---
layout: default
title: Network
description: 
nav_order: 5
parent: AWS
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
* VPC내부의 리소스들은 Internet Gatway, NAT Gatway, VPCE 를 통하지 않으면 S3와 같은 global이나 regional 리소스에 접근할 수 없다.
* internet gateway나 Nat gateway는 S3같은 리소스 접속시에 외부 인터넷에 공개적으로 연결되어서 트래픽이 노출되게 된다.
* S3는 global instance인데 IGW를 이용하지 않고 VPC gateway endpoint (VPCE)를 만들고나서 private link를 이용해서 안전한 연결이 가능하다고 한다.
* VPC Endpoint는 VPC 내부 리소스가 VPC 외부 AWS서비스와 통신할 때, public ip를 사용하지 않고 S3나 Kinesis와 같은 AWS 서비스에 접근할 수 있도록 해준다.

 * 자세한 설명
   + https://aws-hyoh.tistory.com/73

##### interface endpoint
![](/images/aws/VPCE-interface-endpoint.png)
interface endpoint는 1개 이상의 Subnet에 Endpoint Network Interface(ENI)를 생성한뒤에 사설 IP를 부여하고,

ENI를 이용해서 외부 서비스에 접근이 가능하다.

Private DNS names enabled를 활성화 하지 않으면, 공인 IP주소를 불러오므로 주의할 것



##### gateway endpoint
![](/images/aws/VPCE-gateway-endpoint.png)
gateway endpoint는 interface endpoint와는 달리 라우팅테이블에 라우팅만 추가합니다. 자동으로 추가되며 변경할 수 없다.

S3와 DynamoDB 두개 서비스만 사용 가능.