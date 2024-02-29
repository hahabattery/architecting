---
layout: default
title: Overview
grand_parent: AWS
parent: Serverless Application Model
nav_order: 1
---


# Serverless Application Model
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}


Infra와 어플리케이션을 통합해서 관리할 수 있다.

CDK와 다른점은 SAM의 경우 AWS의 Serverless 서비스들에 대해서만 사용 가능하다.


# Resources
 * serverlessland.com
   + serverless app을 공유하고 검증할 수 있는 사이트

 * https://serverlessrepo.aws.amazon.com/applications
   + Serverless Application Repository

 * https://www.readysetcloud.io/blog/allen.helton

 * https://github.com/aws-community-projects

 * AWS Serverless Application Model 2016년 10월 31일 버전의 스펙내용
   + https://github.com/aws/serverless-application-model/blob/master/versions/2016-10-31.md

 * [Introducing AWS SAM Pipelines: Automatically generate deployment pipelines for serverless applications](https://aws.amazon.com/ko/blogs/compute/introducing-aws-sam-pipelines-automatically-generate-deployment-pipelines-for-serverless-applications/)




### **The Complete AWS SAM Workshop**
[The Complete AWS SAM Workshop](https://catalog.workshops.aws/complete-aws-sam/en-US)
 * FROM https://github.com/aws-samples/aws-serverless-workshops

 * APIgateway + Lambda
 * Lambda(nodeJS) 배포시에는 alias를 이용해서 canary deployment를 진행한다.
 * 배포 과정에서 cloudwatch alarm이 발생시에 rollback을 진행한다.
 * 이 워크샵에서는 Cloud9에서 진행하기 때문에, 이미 administrator권한으로 로그인된 환경이어서, IAM 권한이슈는 다뤄지지 않았다.

 * sam local 명령으로 로컬환경에서 동작을 테스트한다.
 * Module 4 내용에 AWS CodePipeline과 Github Action으로 만드는 내용이 잘 설명되어있다.


### **AWS SAM으로 AWS Lambda 배포하기 - Canary와 Linear 배포**
 * https://www.youtube.com/watch?v=2n-2ZzxJZ7Y&t=8s
 * 이 영상은 SAM으로 배포하는게 메인이라서 배포 위주의 내용이다.

### Building a Serverless Post Scheduler For Static Websites
https://www.readysetcloud.io/blog/allen.helton/how-to-build-your-blog-with-aws-and-hugo/

https://www.readysetcloud.io/blog/allen.helton/how-i-built-a-serverless-automation-to-cross-post-my-blogs/

https://github.com/aws-community-projects/blog-crossposting-automation




---
# 정리할 문서

 * https://engineering.surveysparrow.com/build-a-serverless-application-using-aws-lambda-api-gateway-sqs-and-deploy-using-aws-sam-be56a0617a30


 * AWS S3 이미지 압축 처리관련 예
   + https://dev.to/aarongarvey/size-matters-image-compression-with-lambda-and-s3-40bf



---
# AWS IAM Identity Center (SSO)
개발 PC에서 작업할 떄, 전에는 로컬파일에 IAM 에서 발급한 access key를 저장해두었었는데,
**AWS IAM Identity Center** (successor to AWS Single Sign-On) 라는 걸로 작업하면 보안적으로 더 좋다고 한다.

이 서비스를 이용하면, 생성할 사용자가 사용하는 이메일을 입력해서, 내 AWS 자원에 접근할 수 있도록 해준다. Okta와 같은 외부 IDP에도 연결할 수 있다.

설정해보다보니까, 최초에 이 서비스를 Enable하는데 확인 메일이 최대 24시간 걸린다고 한다. 확인 메일로 진행을 완료하면 기존 계정 사용에 제한이 있다고 하는데, 거기까지는 진행해보지 않음.

* 결론
  개인이 테스트용도로 사용하기에는 조금 그랬다...



---
# local test

 * 배포전에 로컬에서 테스트하는 경우에는 로컬에서 lambda가 적용한 컨테이너를 실행해서 테스트하게 된다.

 * 아래 형식으로 명령을 실행하면, 환경변수를 지정해서 동작시키게 된다.
```
sam local invoke -t ./cdk.out/Nov2022BuildersAwsCdkAndAwsSamStack.template.json PutTranslationFunction -e events/putTranslation.json --env-vars environments.json
```

 * lambda같은 경우는 코드가 환경변수의 정보를 이용하는 경우가 많기 때문에, 로컬에서 테스트에도 위와같이 환경변수를 지정하게 된다.

### 주의사항
 * 로컬에서 람다를 실행할지라도, 실제 DB가 AWS에 존재한다면, AWS에는 DB가 저장되게 된다.

### 관련 리소스

 * AWS/webinar/20221121-25/Nov 25-2 [HOL] Testing and Deploying Serverless Application Using AWS CDK and AWS SAM.pdf
   * 위 자료의 githug 주소 => https://github.com/precxist/2022-nov-builders-aws-cdk-and-aws-sam




---
# Logs

 * Fetching Logs by AWS CloudFormation Stack
```
sam logs -n HelloWorldFunction --stack-name mystack
```

 * Fetching Logs by Lambda Function Name
```
sam logs -n mystack-HelloWorldFunction-1FJ8PD
```

 * Tailing Logs
```
sam logs -n HelloWorldFunction --stack-name mystack --tail
```

 * Viewing Logs for a Specific Time Range
```
sam logs -n HelloWorldFunction --stack-name mystack -s '10min ago' -e '2min ago'
```

 * Filtering Logs
```
sam logs -n HelloWorldFunction --stack-name mystack --filter "error"
```




![](/images/aws/sam/sam-01.png)
![](/images/aws/sam/sam-02.png)
![](/images/aws/sam/sam-03.png)
![](/images/aws/sam/sam-04.png)

