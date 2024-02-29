---
layout: default
title: Overview
grand_parent: AWS
parent: Cloud Development Kit
nav_order: 1
---


# Cloud Development Kit
{: .no_toc}

# Table of contents
{: .no_toc .text-delta }

1. TOC 
{:toc}


AWS SAM과 다른점은 CDK의 경우 AWS의 모든 서비스들에 대해서 사용 가능하다.
SAM은 AWS의 Serverless 서비스들에 대해서만 사용 가능하다.


# 리소스들
* https://cdkworkshop.com/ <= 이게 가장 기본적인 워크숍인 듯!
* https://github.com/aws-samples
* https://github.com/aws-samples/aws-cdk-examples
* https://github.com/aws-samples/serverless-patterns


* Serverless Land, AWS Solutions Constructs
  + 간단한 예제

* Construct Hub
  + 복잡하고 심화된 예제
  + 파트너사에서 공유하는 내용?

### 참고할만한 예제
* https://github.com/miztiik/secure-api-with-throttling


**DevOps Hands On Lab 2022-08-17진행**
+ https://catalog.us-east-1.prod.workshops.aws/workshops/cbcd960c-a07b-40c2-a01d-1d2e7a52b945/ko-KR/



# CDK관련 명령

### CDK 설치

 * install and version check

```
npm install -g aws-cdk

$ cdk --version
1.21.1 (build 842cc5f)
```

### 기본명령

 * 초기화 명령

```
cdk init {어플리케이션명} --language {typescript | python | javascript}
```

 * 스택 조회?
 * cdk.json 파일과 같은 디렉토리에서 실행

```
cdk ls
```

 * synthesize(합성하다) one of the stacks

```
cdk synth
```

 * install the bootstrap stack into an environment (account/region)
 * 템플릿 저장을 위한 s3버킷같은 리소스를 만들어주는 작업

```
cdk bootstrap
```

 * 배포

```
cdk deploy {어플리케이션명}
```

 * 변경사항 확인

```
cdk diff
```

 * 어플리케이션 삭제

```
cdk destroy
```


### 자동 빌드
 * package.json 파일에 아래 내용을 추가하고 npm run watch 실행

 ```
 "scripts": {
    ...
    "watch": "tsc -w",
    ...
  },
 ```


### source repository 관련
* github oauth 토큰을 secret manager에 추가하는 명령

```
aws secretsmanager create-secret --name /github.com/binxio --secret-string '{"github-token":"69176fbxxxxxxxxxxxxxxxxxxx22fdac2b78"}'
```


# Constructs
 * cdk docs명령으로 매뉴얼 접근 가능
 * 문서를 보면서, cloudformation 가이드를 보는 것이 도움이 된다.


### Pattern Constructs
 * 자주 사용되는 이용패턴관련 헬퍼

### Intent-based constructs
 * Provide the same functionality with Pattern Contructs
 * but handle much of the details, boilerplate, and glue logic required by CFN constructs.
 * AWS constructs offer convenient defaults and reduce the need to know all the details about the AWS resources they represent, while providing convenience methods that make it simpler to work with the resource.

### CFN Contructs
 * low-level contructs, while we call CFN Resources.


### cross-env
 * cross-env (cross-env ( yarn add -D cross-env or npm i -D cross-env ).)

 ```
 npx cross-env GITHUB_TOKEN=... cdk deploy PipelineStack
 ```

### cdk package 관리

 * local -S (save)

 ```
 npm uninstall -S <package-name>
 ```

 * package.json의 devDependencies 에 있는 패키지인 경우

 ```
 npm uninstall -D <package-name>
 ```

##### global
 * global installed package

 ```
 npm uninstall -g <package name>
 ```



# VS Code

### unresolved import warning - 1
 * Python extension이 설치되있는 상태에서
 * Ctrl + Shift + P
 * "Configure Language Specific" 입력, 엔터
 * "Python" 선택 --> settings.json 열림
 * "python.jediEnabled": false 설정 추가후 vscode 재기동

### unresolved import warning - 2
 * Cmd + Shift + P
 * Python Select Interpreter를 입력하고 맞는 python version을 선택
 * 이렇게 지정한 Python 경로는 vscode 의 개별 과제별 환경 파일인 .vscode 경로 하위의 settings.json 에 기록됨.


# CDK Tip

### 리소스를 stateful 한 것과 stateless 한 것을 분리해라.

* 이렇게 분리하면 stateless한 리소스를 자유롭게 변경하거나 재생성할 수 있게 된다.

### CDK이용시도 termination protection을 이용해라

* 개발자가 다른 계정으로 실수로 로그인해서(동일한 비밀번호로 관리하는 등으로 인해) 삭제하는 것을 막을 수 있다.
* production 환경에서만 termination protection을 하도록 CDK로 코딩하는 것은 간단하다.

### Refactoring은 생각보다 어려울 수 있다.
* Logical ID가 변경되면, 리소스가 삭제되고 추가되기 때문에 매우 조심해야한다.

* 이러한 것을 막을려면 개발자의 다른 AWS계정으로 변경전 버전을 배포해놓고, Refactoring한 버전을 배포해보면, 확인할 수 있다.

### IAM 편의 메소드를 이용하자
* 보통 grant로 시작하는 메소드 이름을 사용할 수 있다.
* dynamoDB 테이블에 read-only 접근을 하고 싶은 경우는 아래 처럼 작성 가능하다.

```
myDynamoTable.grantReadData(myLambdaFunction);
```
* IAM Role을 생성하려고 하는 등의 작업시에는 편의 메소드가 있는지, 한번 더 하자.

### 추상화는 성급하게 하지 말자.

* L1은 AWS CloudFormation에 정의된 그대로의 리소스를 생성한다.
* L2는 상위레벨로 단순한 리소스 생성이 아닌, 특정 기능을 제공한다(intentioinbased API). L2는 코드 틀(boilerplate)나 default 설정이나 CDK작성자가 필요로 할만한 관련 로직(glue logic)이 준비되어있다.
* L3 하나의 완결된 작업을 할 수 있도록 설계되어있다. 다양한 리소스를 이용하도록 작성된 경우가 많은데, 일종의 Optionated pattern으로 볼 수 있다. (Optionated pattern; 패턴을 만든 사람이 자기가 필요하다고 생각한 여러 리소스나 로직을 사용자의 의견을 고려하지 않고 작성하였다는 의미...)

* L3와 같은 패턴을 새로 작성하려는 경우가 생기겠지만, L2의 유연성을 줄이거나, 새로 작성된 L3의 shared construct의 default property 를 변경하는 경우에 혹시 다른 어플리케이션에 영향을 줄 수 있으므로 주의해야 한다.

### Programming Language별로 다른 내용

##### Python

 * virtual environment
   * allow you have a self-contained, isolated environment to run Python and install arbitrary packages without polluting your system Python.

```
// MacOS or Linux
source .env/bin/activate

// Windows
.env\Scripts\activate.bat

// Virtual Environment가 활성화된 상태에서 종속 패키지 설치
pip install -r requirements.txt

```

##### Java

```
// maven
mvn package
```


# Trouble Shooting

### This CDK CLI is not compatible with the CDK library used by your application.

 * CDK CLI를 최신으로 업데이트

```
npm install -g aws-cdk@latest
```


### Argument of type 'this' is not assignable to parameter of type 'Construct'.

 * package.json 파일에 cdk library를 같은 버전으로 업데이트
 * node_modules 삭제
 * package-lock.json 삭제
 * npm install
 * IDE 재시작


### Cannot find module 'typescript'

 * https://stackoverflow.com/questions/44611526/how-to-fix-cannot-find-module-typescript-in-angular-4

 ```
 npm install -g typescript
 npm link typescript
 ```

### failed bootstrapping: Error: Please pass '--cloudformation-execution-policies'

```
npx cdk bootstrap --profile closeyes2 --cloudformation-execution-policies arn:aws:iam::aws:policy/AdministratorAccess
aws://ACCOUNT1/us-east-2
```


