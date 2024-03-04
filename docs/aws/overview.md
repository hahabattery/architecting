---
layout: default
title: Overview
description: summarize the overall content about AWS.
nav_order: 5
parent: AWS
---


# Overview
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}



# Resources
 * [AWS Startup](https://www.awsstartup.io/)


* https://kr-resources.awscloud.com/aws-industry-week-2023?trkCampaign=industry-week&trk=cedd1ded-dc2a-44c3-bcf5-1bfc6767ed12&sc_channel=em&mkt_tok=MTEyLVRaTS03NjYAAAGPAx0UWwHvqi4NFBbPCPGau593kevR1KSiOopH7QN5q1zDgEzrOKYi-TSZutML27royE_dBx9BeXYLsHoV0sQin-khCun-ySQdKgA6VPj6CvFVYobTTrUauA
  + => 2023년도 industry week

* https://aws.amazon.com/ko/events/seminars/aws-industry-week/
  + => 2022년도 industry week

* https://serverlessland.com/
* https://aws.amazon.com/ko/event-driven-architecture/

### 리소스 허브
 * https://kr-resources.awscloud.com/ <= 생각보다 잘 정리되어있는 듯 하다.
 * https://kr-resources.awscloud.com/aws-modern-applications-kr

### 데이터 리소스 허브
* [https://resources.awscloud.com/data-resource-h](https://resources.awscloud.com/data-resource-hub)


# 스킬빌더

https://explore.skillbuilder.aws/learn

---
# 리소스
[AWS Whitepapers](https://aws.amazon.com/ko/whitepapers/?whitepapers-main.sort-by=item.additionalFields.sortDate&whitepapers-main.sort-order=desc&awsf.whitepapers-content-type=*all&awsf.whitepapers-global-methodology=*all&awsf.whitepapers-tech-category=*all&awsf.whitepapers-industries=*all&awsf.whitepapers-business-category=*all)

[AWS Blog](https://aws.amazon.com/ko/blogs)

 * [AWS 고객 성공 사례](https://aws.amazon.com/ko/solutions/case-studies)
   + 홍보위주의 자료라서, 딱히 유용한 정보는 없어보임...

[웨비나 다시보기](https://kr-resources.awscloud.com/aws-builders-korea-program)

 * AWS 가 제공하는 여러가지 Open Data
   + https://registry.opendata.aws/
   + 샘플(New York City Taxi and Limousine Commission (TLC) Trip Record Data)
     + https://registry.opendata.aws/nyc-tlc-trip-records-pds/

[아키텍처 관련 모범 사례를 사용해 학습, 측정 및 구축](https://aws.amazon.com/ko/architecture/well-architected)

---
# AWS의 장점
AWS의 설명 매우 세세하게 나눠진 modular API를 이용해서 고객맞춤형 서비스를 구축할 수 있다. 이게 최근에 경쟁자로 떠오르는 AZURE나 GCP와의 차이라고 할 수 있을 거 같다.




---
# Code Whisperer
> 코딩하는동안 주의가 산만해지는 것은 끊임없는 문제입니다.특히 웹에서 코드샘플 과 문서를찾기위해 컨텍스트를 전환해야하는 경우에는 더욱 그렇습니다. Amazon CodeWhisperer는 필요할 때 자동으로 유용한 제안을 제공하여 코드에 집중할 수 있도록 해주므로 편집자를 떠날필요가 없습니다.

Ryan Grove Staff Software Engineer, SmugMug


---
# 로드맵
AWS는 대부분의 서비스에 대해서 로드맵을 제공하지 않는다.

https://github.com/aws-cloudformation/aws-cloudformation-coverage-roadmap

https://github.com/aws/containers-roadmap

https://github.com/aws/elastic-beanstalk-roadmap/projects/1

https://github.com/orgs/opensearch-project/projects/1

https://www.udemy.com/course/build-a-serverless-app-with-aws-lambda-hands-on/tg


# Resource Location

 * AWS 자원 레벨 이라고도 함.
 ![](/images/aws/AWS자원레벨.png)

 * Amazon 로 시작하는 서비스는 프리미티브 서비스
 * AWS 로 시작하는 서비스는 프리미티브 서비스를 연결해주고 관리해주는 서비스

 * https://www.udemy.com/course/aws-serverless-a-complete-guide

---
# AWS 지원 프로그램
AWS에는 다양한 지원 프로그램이 존재한다.
* dynamoDB 의 스키마 설계관련 지원
* 보안 지원 프로그램이 있다.
  + webinar/20230713-발표자료_클라우드의_시작__AWS_보안_지원_프로그램_알아보기.pdf  <= 요 파일 참조해보자.

---
# 서비스 글로벌 런칭관련
* AWS LOCALzone이나 outpost를 설치하면, AWS의 글로벌 백번에 연결된다.
* Outpost 를 쓰는 대다수의 이유는 금융정보 residency (해외로 반출할 수 없다)
  + 관련 키워드) "data residency"


---
# ECR
ECR image 저정뿐만아니라, signing해서 보안적으로 향상됨.


---
# Cloud Map
진화된 형태의 DNS

netflix는 유레카라는 걸로 비즈니스로직을 export함.


 * VPC는 SDN(software defined network)라고 한다.

 * NAT gateway는 일방향 연결을 가능하게 하기 때문에, 보안상 이용할 때가 있음.

 * direct connect 는 aws와 on premise를 연결할 때 이용함.

 * VPC는 브로드캐스트를 지원하지 않음.

 * IGW는 일대일 NAT로 내부에서 외부와 통신을 하려면, 퍼블릭 아이피가 있어야 한다.

 * subnet간에는 라우팅을 하지 않으면, default라우팅에 영향을 받고,
   기본적으로 설정되어있지 않으면, 트래픽이 전달되지 않는다.

 * 보안 솔루션이나 monitoring 솔루션을 이용시에 routing으로 트래픽을 제어함.

 * NACL
   * subnet 단위이다.
   * 보안그룹하고 달리 stateless해서 inbound와 outbound모두 설정해야 된다.

 * security group
   * stateful로 inbound를 설정하면, outbound는 자동적용된다.

 * VPN게이트웨이 on premise와 aws사이에 SSL같은 암호화 통신을 지원할 때 사용되는듯.



 * VPC flow logs 라고 하는게 있는데, cloud watch logs group에 기록됨 (10분 ~15분 딜레이 된다고 함.)



 * Kafka
   * 관리상의 편의를 위해, Kinesis 사용 추천
   * Kinesis 는 각각의 가용영역이 다르다고 하더라도 혹은 subnet이 다르더라도
   * 동일한 Region 내에서는 latency가 동기화용으로 사용 가능 할 정도로 제공됨


 * EC2 instance 장애 대처 관련
   * https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html
   * 장애시 별도의 알림을 생성 가능 : EC2 서비스 - instance - monitoring - Cloudwatch Alarm 생성
   * 드물지만, 인스턴스 자체적으로 물리적인 장비 문제인 경우, reboot 가 아닌, stop&start 로 재시작하는 방법으로, 다른 서버에서 시작 가능
   * EC2 와 EBS 는 서로 다른 서비스로 운영이 되고 있어서, EC2가 문제가 생기더라도 EBS에 있는 데이터는 별도로 보존&사용 가능
   * EBS자체적으로 문제가 생기는 경우도 아예 없지는 않음. EBS Policy Schedule에서 lifecycle를 설정해 두는 것을 권장


 * IAM 유저별로 Management console 에서 EC2 인스턴스 접근 권한을 설정하는 방법
   * https://aws.amazon.com/ko/premiumsupport/knowledge-center/iam-ec2-resource-tags/


 * EC2의 Dynamic Port 관련 방식
   * https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html
   * 계속해서 바뀌는 포트들을 별도의 DB에 저장하고, 이를 Lambda로 변경이 될때마다 이벤트를 받아서 처리하는 방식으로 구현 가능



 * 보안 관련
   * WAF를 통해서 IP 차단 및 관리가능. Shield Advanced 없이도 사용 가능.
   * Guard Duty : 일단은 가볍게 사용하셔서 공격에 대한 정보들을 알아 볼 수 있음
   * Shield Advanced
     * 월 3K$, 1년 약정 -> 매달 3K를 지불하시게 됩니다.
     * 추가적으로 DRT 팀이 붙어서 관리를 해주고, 공격 발생시 완화해주는 추가적인 서비스제공

 * AWS shield가 적용되는 서비스가 cloud  front, elb등등으로 정해져 있는데, public ip가 할당되있는 EC2도 적용된다고 함.

 * 네트워크 관련
   * EC2 인스턴스 타입별로 network성능이 다르기 때문에, 성능 병목은 없는지 확인 필요


 * ELB의 경우에 scale out으로 instance가 늘어나는 경우도 있고, scale up으로 성능만 올라가는 경우가 있다.

 * ELB 취소 시 인플라이트,드레이닝 시간?
   * 기존에 들어온 요청들이 완료되도록 기다려 주는 시간

 * 배치그룹
   * 서버락하나에 모은다고 들음
   * 가능한 물리적으로 모여있는 서버들을 제공
   * 성능이 중요한 빅데이터 시스템의 경우에 사용


 * ALB , NLB
   * Http 통신속도는 기본적으로 NLB가 좀더 가볍게 도는 편, 스케일링도 좀더 빠지만
   * 기능적으로 ALB가 유용하게 잘 사용되기는 해서 일반적으로 많이 권장이 됨

 * CLB
   * autoscailing이 어렵고, freewarm을 해야했다, massive한 트래픽의 경우에는 ALB를 추천함.

 * ALB
   * layer 7기반에 컨텐츠기반 라우팅이 가능(path기반, host기반)
   * URL기반으로 하나의 ALB에서 load balance가 가능하기 때문에 path기반이 유용함.
   * host기반으로 하면, 이름별로 나뉘어진 그룹의 호스트별로 load balancer가 필요하다.

 * NLB
   * L4기반 TCP용
   * EIP부여 가능(타장비와 호환상 필요한 경우)
   * 커넥션이 긴경우 추천됨.
   * 게임이나 메세징 서비스
   * port기반으로 트래픽을 처리함
   * TLS termination지원(앞단에서 SSL처리함)

 * ELB에서 sticky session기능을 제공함
   * 뒤의 서버단에서 공유메모리에 세션정보를 저장해서 stateless하게 처리하지 못하는 경우

 * ROUTE 53
   * 100% SLA
   * private DNS기능이 있음.


 * Cloudfront
   * 캐시 기능을 통해서 해외 사용자들이 크게 개선된 속도 혜택을 볼 수 있음
   * Invalidate 해서 강제로 새로 배포 가능
   * AWS 내부 백본망을 통해서 전송하기때문에 속도도 빠르고 안정적임
   * Cloudfront 에서 lambda edge를 사용 할 수 있기 때문에,
     * 서버 자원을 아끼는 동시에, 들어오는 요청들에 대해 추가적인 (권한처리 등등)작업을 진행 할 수 있음.


---
# Cloud 9

 * Cloud 9 사용시 생성되는 인증정보는 AWS에 로그인한 IAM User의 크레덴셜이다. 아래 명령으로 확인할 수 있다.

```
aws sts get-caller-identity
```

 * ensure third-party cookies is enabled



# 질문 했었던 내용들
```
auto scailing 관련 문의

auto scailing로 카나리 릴리스 배포같은 거는 없나?
=> code deploy


edge optimized: cloudfront의 팝서버로 글로벌 사용자가 접근할 때 빠르다.


람다는 메모리설정량에 따라서 CPU성능이 좌우된다.

컨테이너위에 람다가 올라오는 구조이기 때문이기 때문에, 코드패키지 사이즈를 줄이고,
lambda handler와 core logic 을 분리
...


provisioned concurrency  가 새로나온 기능으로 미리 띄워놓는 기능으로  cold-start를 줄인다.


cognitor 사용자풀은 사용자 관리해주는 기능. email이나 전화번호 확인등,  multi factor authentication도 지원한다.
cognitor는 인증을 위한 JWT토큰을 발급해서 뒷단의 백엔드 서비스를 이용할 수 있다.




청중 질문

질문: transit gateway 와 direct connect 연결시 1G 이하로는 사용이 불가능 한가요?

답변: 네 불가능 합니다..  Transit Gateway와 연결하는 Transit VIF는 Host connection 연결(10m, 50m 등)은 아직은 어렵습니다.



질문: MSK 이용시에도 VPCE를 사용하도록 가능한가요? 이렇게 사용하면, 요금관련해서 효과적인가요?

답변: 요금 보다는.. Private 연결을 위해서 사용합니다. MSK 를 사용하시는 서비스가 Endpoint 서비스가 가능한지 체크 하시고 사용하시면 됩니다



질문: CLB같은 경우는 프리웜을 사용하는 유스케이스를 많이 보았는데, ALB나 NLB도 프리웜을 요청하면 처리해주시나요?

답변: 네. 가능합니다.



청중 질문

질문: 고객중에, 방화벽에 화이트리스트 IP를 지정하여 통신을 하고 싶어하는 곳이 있습니다. 이때 공인IP를 고객에게 전달해야 할텐데 ALB를 사용하여 https를 서비스 한다면 고객에게 변동되지 않는 공인IP를 알려주어 화이트리스트로 지정하게 할 수 있을까요?

답변: NLB + ALB 조합으로 가능합니다..



=======================================================


질문: 1000G 사이즈의 EBS가 있어도, 스냅샷을 생성하면, 실제 사용하는 100G만 요금이 청구되나요?

답변: 네 스냅샷은 사용되는 용량만큼만 비용이 청구됩니다. 그래서 100G만이라고 보시면 됩니다.


질문: EBS도 IT장비이다 보니, 예를들면 배드섹터같은 장애가 있을 수도 있을거 같은데, 이런 경우 AWS에서 감지해서 사용자에게 특정액션(EC2의 stop and start같은)을 취하라는 Notification같은거를 주는지 궁금합니다.

답변: EBS에서 제공해주는 볼륨은 하나의 물리디스크가 아닌 대형 스토리지의 일부 논리볼륨을 떼어서 제공해주는 것입니다. 그리고 배드섹터 감지는 물리측면에서 진행되기 때문에 EBS에의 영향도는 적습니다. 하지만 EBS 관련 이슈가 발생한다면 cloudwatch에 로그가 기록될 것입니다. 이를 기반으로  notification 기능 설정이 가능합니다.


=======================================================


DR구축검토중에 궁금했던 내용인데요. 한국 리전내의 AZ영역이 다른경우에는 데이터센터가 다른 것이 보장되는지 궁금합니다.

답변: AWS의 물리적인 실제 데이터 센터위치는 AWS의 보안사항이라 외부에 오픈이 되지는 않습니다만, AZ는 물리적으로 독립된 데이터센터의 집합으로 각 AZ는 장애 및 재해에 대해 독립적인 물리적인 위치에 배치되는 것으로 이해하시면 됩니다.


```
