---
layout: default
title: AWS Workshops
nav_order: 5
parent: AWS
---


---
# 리소스

* AWS 워크샵 모음
  + https://workshops.aws/  <= 전체 리스트
  + https://aws.amazon.com/ko/blogs/korea/aws-korean-hands-on-labs-guides/
  + aws-builders-kr.workshop.aws
  + https://aws-media.gitbook.io/aws-immersion-day/
  
---
# Cognito
https://catalog.workshops.aws/wyld-pets-cognito/en-US





---
# Wild Rydes unicorns
FROM https://github.com/aws-samples/aws-serverless-workshops


**aws-serverless-workshop - WebApplication**
 * https://webapp.serverlessworkshops.io/
 * 코드 저장소는 AWS Commit 이용
 * Front는 vueJS 로 작성하고, Amplify로 배포
 * Backend는 API Gateway + Lambda(Proxy) + DynamoDB
 * 인증은 Cognito User Pool 이용해서, 회원 가입이나 JWT 토큰 발급을 함.
 * Front에서 Cognito를 이용하는 부분은 Amplify JavaScript library를 이용해서 작업이 되어있다.


**aws-serverless-workshop - Auth**
 * https://auth.serverlessworkshops.io
 * 관련 리소스는 깃헙에 저장되있음.
   * https://github.com/aws-samples/aws-serverless-auth-workshop
 * Front는 ReactJS
 * Cognito User Pool을 Amplify library를 이용해서 인증처리한다.
 * AWS Amplify client library 를 사용하면, authentication하고 authorization이 동작한다.


**aws-serverless-workshop - Data Processing**
 * https://data-processing.serverlessworkshops.io/
 * Data Lake를 만들고, Athena를 이용해서 ad-hoc 쿼리로 이정보를 조회하는 내용.
 * RDS에서 데이터를 AWS Glue를 이용해서 ETL로 S3에 csv로 저장하고
 * 이 정보를 Redshift에서 조회하는 내용


**aws-serverless-workshop - DevOps**
 * https://cicd.serverlessworkshops.io/ (deprecated) => The Complete AWS SAM Workshop 으로 이동함


**aws-serverless-workshop - Image Processing**
* https://image-processing.serverlessworkshops.io/
* lambda(nodeJS)와 step function서비스를 이용한 lambda 함수의 ochestration이 다뤄진다.
* 이 워크숍에는 step function에 대한 이해하기 쉬운 설명이 들어있다.
  * https://image-processing.serverlessworkshops.io/implementation/state-machine.html
* step function을 json으로 정의 하는 내용도 참고할 만 한 거 같음.
* 아래 기능들도 사용법이 나와 있음
  * SNS 토픽 전송 기능

* 이렇게 step function을 다른 서비스와 연동할 수 있다.
  * https://docs.aws.amazon.com/step-functions/latest/dg/concepts-service-integrations.html

* 워크샵을 진행하면서 생성한 stepfunction을 병렬로 동작시키는 부분도 후반부에 포함되어 있음.


**aws-serverless-workshop - Multi Region**
 * https://github.com/aws-samples/aws-serverless-workshops/tree/master/MultiRegion

 * 정적 컨텐츠를 S3에 저장
 * Lambda(NodeJS) + API Gateway(CORS설정 내용도 있음) + CloudFront
 * 클라이언트 소스는 Angular에 TypeScript로 작성되어있음.
 * Route53 서비스에 Failover 설정하는 내용(이 내용은 다른 서비스 개발시에 유용할 듯.)

 * 고객의 티켓ID를 dynamoDB 에 저장
 * Multi Region으로 구성하는 방식
   * DynamoDB의 Global Tables
   * S3에 Cross Region Replication 이라는 기능이 있지만, 이 워크숍에서는 사용되지 않음.


**Wild Rydes Serverless Workshops - Security**
 * https://github.com/aws-samples/aws-serverless-workshops
 * (슬라이드)[./SVS305-Secure-Serverless.pdf]
 * cloudformation 으로 VPC + RDS + API Gateway + Lambda 를 관리하는 예

```
aws cloudformation package --s3-bucket cloudformation210823-1 --template-file template.yaml --output-template-file template-generated.yam

aws cloudformation deploy --template-file /home/ec2-user/environment/aws-serverless-security-workshop/src/template-generated.yaml --stack-name serverless-workshop --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
```


---
# Well-Architected Labs <= 이것도 랩이다!
https://wellarchitectedlabs.com/

[아키텍처 관련 모범 사례를 사용해 학습, 측정 및 구축](https://aws.amazon.com/ko/architecture/well-architected)


최근에 웨비나에서 확인했던 워크샵

https://wellarchitectedlabs.com/reliability/200_labs/200_testing_for_resiliency_of_ec2/


### DR

**DR (재해/복구)** 관련 블로그들

https://aws.amazon.com/ko/blogs/architecture/tag/disaster-recovery-series/

---
# Serverless 관련 워크숍
**AWS 서버리스 애플리케이션의 진화 : 대용량 트래픽도 서버리스로 처리할 수 있습니다!**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/05e3e1f9-5d5a-4cc5-9899-df114def68e7/ko-KR
+ Python3.8이용
+ 성능 테스트 관련 내용도 포함됨.

**Serverless Workshop(배달 주문 서비스 만들기)**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/4923c0ff-6470-46e1-9884-7c6ee63e7136/ko-KR
+ AWS Serverless 들의 기본이 되는 Lambda, API Gateway, SQS, SNS, Event Bridge 를 이용한 PoC

**AWS Step Functions Workflow Studio Workshop**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/19701223-0bf3-4273-a75a-209aa242f8a8/ko-KR

**API gateway workshops**
+ https://catalog.workshops.aws/general-immersionday/en-US/basic-modules/20-vpc/api-gateway
+ APIgateway 사용시에 lambda를 proxy모드가 아닌 설정으로 사용하는 예제
+ Message Transformation에 대한 좋은 설명 => https://catalog.workshops.aws/general-immersionday/en-US/basic-modules/20-vpc/api-gateway/3-apigateway#transform-response-payload
+ EC2 Autoscaling 설정도 참고할 만함.

**Amazon Lambda URL 로 API Server 만들기**
+ https://linen-lizard-436.notion.site/2022-AWS-Builders-Compute-de42b3989ed04bc294dd6cba0a06688f

**Web application with Serverless**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/600420b7-5c4c-498f-9b80-bc7798963ba3/ko-KR/serverless
  + => API Gateway + Lambda + DynamoDB로 구축하는 예

+ https://catalog.us-east-1.prod.workshops.aws/workshops/600420b7-5c4c-498f-9b80-bc7798963ba3/ko-KR/ec2
  + => ELB + EC2 로 VPC를 구성하는 예
  + => SNS를 이용해서 알람을 메일로 보내는 처리
  + => Launch Template을 설정해서 Auto Scaling Group을 설정하는 내용도 포함됨

> 질문: 예전에 ELB를 public 서브넷에 구축하고, EC2를 private 서브넷에 구축하는 경우가 있었는데요. 이렇게 구성하면 트래픽 속도가 느리던데요(통신은 잘 됩니다)
> 이런 구성이 권장되지 않는건지, 아니면 어떤 설정이 문제가 있을 수 있을까요?
> 답변: 말씀하신 방법이 권장되는 방법입니다. 트래픽 속도가 느린 부분은 다른 이유가 있을 것으로 추측됩니다.


**AWS Alien Attack: A Serverless Adventure**

AWS Alien Attack 워크샵에서 API Gateway를 proxy모드가 아닌 integration 형태로 설정( Mapping Templates  to have data transformations executed by API Gateway)하고 있어서 참고할 만한 것 같다(실제로 업무에서 어떤 형태로 사용하는지는 잘 모름...).
+ https://alienattack.workshop.aws/
+ https://catalog.us-east-1.prod.workshops.aws/workshops/fc36cb5a-de1b-403f-84dd-cc824390c548/


**AWS Alien Attack: Short Labs**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/3ae476e4-e66d-4e78-b22f-6190c79ddee2/en-US

**AWS Step Functions Workflow Studio Workshop**
  + https://catalog.us-east-1.prod.workshops.aws/workshops/19701223-0bf3-4273-a75a-209aa242f8a8/ko-KR

### APIgateway 설정관련

+ https://www.youtube.com/watch?v=ksT_ltGwMic <= API gateway 유효성 체크관련 동영상
+ https://www.youtube.com/watch?v=q3eI1I33xFE <= API gateway 용청/응답 메시지를 변환하는 방법관련 동영상


### event driven 시스템 관련

**Building event-driven architectures on AWS**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/63320e83-6abc-493d-83d8-f822584fb3cb/ko-KR


**Iterative App Modernization Workshop2022-08-17진행**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/f2c0706c-7192-495f-853c-fd3341db265a/ko-KR


### Serverless 구성 관련 글
* https://medium.com/serverless-transformation/eventbridge-storming-how-to-build-state-of-the-art-event-driven-serverless-architectures-e07270d4dee
* https://medium.com/serverless-transformation/eventbridge-the-key-component-in-serverless-architectures-e7d4e60fca2d




---
# 데이터 분석 관련
**Coursera무료 강좌 - Practical decision making using no-code ML on AWS**
+ https://www.coursera.org/learn/no-code-ml-aws

**Self-Study-On-SageMaker**
+ https://github.com/gonsoomoon-ml/Self-Study-On-SageMaker/

**공식 AWS 머신 러닝의 교육 에 사용되는 많은 유용한 링크가 있는 Git**
+ https://github.com/serithemage/AWS_AI_Study

**SageMaker JumpStart**
+ https://docs.aws.amazon.com/ko_kr/sagemaker/latest/dg/studio-jumpstart.html
미리 만들어진 솔루션, 알고리즘 등이 있어서 샘플코드를 가지고 사직하기에 좋습니다. 개발자 가이드 문서이니 한번 보시는 것을 권장 드립니다.

**SageMaker: Starting XGBoost with SageMaker V2.24**
+ https://github.com/comeddy/Start-SageMaker
  매우 기초적인 워크샵
+ https://wooono.tistory.com/97  <= XGBoost 개념에 대한 설명

**다이렉트 마케팅 (SageMaker 내장 알고리즘 XGBoost 사용)**
+ https://github.com/aws-samples/aws-ai-ml-workshop-kr/tree/master/sagemaker/xgboost
  매우 기초적인 워크샵

**AWS AIML 한글 워크샵 & 예제 모음**
+ https://github.com/aws-samples/aws-ai-ml-workshop-kr

**SERVERLESS DATA PROCESSING ON AWS**
+ https://data-processing.serverlessworkshops.io/


**MSK 워크샵**
 + [Amazon MSK Labs](https://catalog.us-east-1.prod.workshops.aws/workshops/c2b72b6f-666b-4596-b8bc-bafa5dcca741/en-US/mskconnect/overview)

**Amazon Redshift Deep Dive Workshop**
+ https://redshift-deepdive.workshop.aws/

redshift관련 워크샵모음집 같은 거라서 그냥 하기에는 조금 난이도가 있는 듯.

**SageMaker Studio Immersion Day**
* https://sagemaker-immersionday.workshop.aws/
* https://catalog.us-east-1.prod.workshops.aws/workshops/63069e26-921c-4ce1-9cc7-dd882ff62575/en-US

**Amazon Personalize 기반으로 실시간 추천 사이트 만들기**
  + https://catalog.us-east-1.prod.workshops.aws/workshops/ed82a5d4-6630-41f0-a6a1-9345898fa6ec/ko-KR

**IoT Data Analytics**
  + https://catalog.us-east-1.prod.workshops.aws/workshops/ab3610a3-ebcb-48ee-9f9d-be5a37bfe6a8/ko-KR

**AWS Builders Korea - IoT workshop**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/1ce44788-8018-4238-b63e-690f66769fa4/ko-KR

**Analytics on AWS**
* https://catalog.us-east-1.prod.workshops.aws/workshops/44c91c21-a6a4-4b56-bd95-56bd443aa449/ko-KR
* AWS Glue, AWS Glue Studio, AWS Glue DataBrew, QuickSight, Redshift를 이용하는 내용

**Amazon SageMaker와 CDK를 활용한 딥러닝 모델 서비스 현대화 기법 – 최권열:: AWS Innovate 2021**
+ https://www.youtube.com/watch?v=kn2DBjZW5W8

**Amazon AppFlow Workshop**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/9787ec94-1ace-44cc-91e5-976ad7ddc0b1/en-US

+ <= Google Analytics 데이터를 S3로 이동시키는 처리가 들어있음.

+ 관련 블로그 <= https://aws.amazon.com/ko/blogs/big-data/analyze-google-analytics-data-using-upsolver-amazon-athena-and-amazon-quicksight/


---
# Amplify
**Sample AWS Amplify SageMaker React app**
+ https://github.com/perima/amplify-sagemaker

+ A Sample amplify react single page app that invokes a sagemaker endpoint for inference. The sagemaker endpoint is not part of this project and needs to be setup separately.

**Full Serverless App with React and Amplify**
  + https://master.d3f5073vvso9t3.amplifyapp.com/lab0/
  + 클라이언트 개발자가 CLI툴로 백엔드 서비스들을 결햅시켜서 웹서비스를 개발할 수 있다.
  + amplify studio 라고 웹에서 amplify를 개발할 수 있는 콘솔도 제공하고 있다.

---
# Data Store

### RDS
**Amazon Aurora Labs for MySQL**
+ https://awsauroralabsmy.com/

**Amazon Aurora Labs for PostgreSQL**
+ https://aurora-pg-lab.workshop.aws/

**Purpose Built Databases Workshop(DynamoDB, Aurora)**
+ https://amazon-rds-purpose-built.workshop.aws/

**AWS Builders Database 200 실습**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/728bbc3c-65f8-4549-ab4a-7f4d6d136a97/ko-KR

**AWS 기반 데이터분석 파이프라인 구축 - Analytics on AWS 워크숍**
+ https://khw742002.tistory.com/33


### S3
**AWS datasync를 활용한 NFS to S3 마이그레이션**
+ https://jk0-3.gitbook.io/aws-builders-online-strageworkshops/
+ pdf 파일의 "20230322 - AWS Storage.pdf" "20230322 - AWS Storage(workshop).pdf" 을 참조.
+ 아마도 Storage Gateway 라는 것이 S3파일을 NFS처럼 접근할 수 있도록 해주는거 같다. 어플리케이션의 수정없이 파일 을 on premise에서 클라우드로 마이그레이션할 수 있다!

---
# DevOps

**CICD 워크샵**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/3533fdca-eaf6-4a96-96fc-58d8a7ebf5f4/ko-KR
+ "기본" "심화" 이렇게 나눠져 있는데, ECS 환경에서의 배포 내용이 위주인거 같다.

**DemoGo - Cats and Dogs (ECS워크샵)**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/8c9036a7-7564-434c-b558-3588754e21f5/ko-KR/


---
# ECS, EKS

* EKS 워크샵
  + https://awskocaptain.gitbook.io/aws-builders-eks/

* EKS 워크샵(종류많음)
  + https://www.eksworkshop.com/

---
# 네트워크

### WAN
* global private network를 구축하는 내용
* 최적화된 AWS-지점 간 연결
* 네트워크 관리 단순화
* 세그멘테이션기능을 이용한 망분리


https://catalog.us-east-1.prod.workshops.aws/workshops/d42566be-6428-4f01-8df1-3f987a357ab5/ko-KR

이 워크샵은 Cloud Stack 생성후에, 다시 Cloud9에서 terraform을 실행 해서 진행을 한다.

글로벌 네트워크를 구축했을 때, 웹서비스를 한다고하면, 도메인서비스에서 지리적으로 근접한 웹서버에 트래픽을 보내도록 해줘야 하는데... 이건 Route53의 지리적 정보를 이용한 routing을 이용하면 될 수 있을수도.

---
# 보안 관련

* GuardDuty
  + https://catalog.us-east-1.prod.workshops.aws/workshops/dfa6471a-32be-4b06-96c3-ea5e650fefda/ko-KR

* WAF 워크샵(검색하다 나온 것)
 + https://catalog.us-east-1.prod.workshops.aws/workshops/c2f03000-cf61-42a6-8e62-9eaf04907417/en-US/00-introduction   <= 요게 최신(한글 버전은 아직 없다)
 + https://protecting-workloads.awssecworkshops.com/
 + [AWS WAF 공격 및 방어 실습](https://sessin.github.io/awswafhol/)

* Guard Duty Lab
 + https://catalog.workshops.aws/guardduty/en-US/introduction

**Serverless Security Workshop**
+ https://github.com/aws-samples/aws-serverless-security-workshop

* https://aws-quickstart.github.io/quickstart-keycloak/
  + https://aws.amazon.com/ko/quickstart/architecture/keycloak/
  + https://aws-quickstart.github.io/quickstart-keycloak/

---
# Video
https://aws-media.gitbook.io/aws-immersion-day/vod/aws

* 동영상관련(Elemental Media) 워크샵
  + https://aws-media.gitbook.io/aws-immersion-day/



---
# 기타 워크
 * Amazon CloudFront를 사용하여 아키텍처 개선하기
   + https://catalog.us-east-1.prod.workshops.aws/workshops/4557215e-2a5c-4522-a69b-8d058aba088c/ko-KR

 * VPC 네트워크 (**transit gateway**관련 내용도 포함)
   + https://catalog.workshops.aws/aws-vpc-networking/ko-KR
>질문: multi account환경에서도 trasit gateway를 이용해서 연결할 수 있나요?
>
>답변: 네 다중 계정 지원이 올해 부터 지원 가능 합니다.  https://aws.amazon.com/ko/about-aws/whats-new/2022/05/multi-account-support-aws-transit-gateway-network-manager/
