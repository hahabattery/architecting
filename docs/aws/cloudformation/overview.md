---
layout: default
title: Overview
grand_parent: AWS
parent: CloudFormation
nav_order: 1
---

 
 
 * YAML에서 - 는 array를 의미한다.


 
 
 * resource list
   * https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html

 * EC2 information
   * https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html


# 유용한 명령어

### cloudformation validate

 * full path

```
aws cloudformation validate-template --template-body file:///home/local/test/S3_Bucket.template
```

 * relative path

```
aws cloudformation validate-template --template-body file://S3_Bucket.template
```

### parameter

 * parameter를 인자로 문자열로 지정하는 경우

```
aws cloudformation create-stack --stack-name test-200808 --template-body file://1-ec2-with-sg-eip.yaml --parameters ParameterKey=SecurityGroupDescription,ParameterValue=test
```

 * parameter를 파일로 지정하는 경우

```
aws cloudformation create-change-set --stack-name example --template-body file://templates/instance-and-route53.yml --parameters file://parameters/instance-and-route53.json --change-set-name changeset-1
```



# AWS CLI

### \cloudformation-examples-2\templates\single-instance.yml
 * launching the stack

```shell
$ aws cloudformation create-stack --template-body file://templates/single_instance.yml --stack-name single-instance --parameters ParameterKey=KeyName,ParameterValue=tutorial ParameterKey=InstanceType,ParameterValue=t2.micro
```

 * deleting the stack

```shell
aws cloudformation delete-stack --stack-name single-instance
```

### \cloudformation-examples-2\templates\instance-and-route53.yml

 * launching the stack

```shell
aws cloudformation create-stack --template-body file://templates/instance_and_route53.yml --stack-name route53 --parameters ParameterKey=KeyName,ParameterValue=tutorial ParameterKey=InstanceType,ParameterValue=t2.micro ParameterKey=HostedZoneName,ParameterValue=sub.tongueroo.com. ParameterKey=Subdomain,ParameterValue=testsubdomain
```

 * simplifying command (별도 paramenter 파일에 파아미터를 정의해서 이용)

```shell
aws cloudformation create-stack --template-body file://templates/instance-and-route53.yml --stack-name route53 --parameters file://parameters/instance-and-route53.json
```

### Updating

 * single-instance.yml -> instance-and-route53.yml

```
$ aws cloudformation update-stack --stack-name example --template-body file://templates/instance_and_route53.yml --parameters file://parameters/instance_and_route53.json
```

### Change Set

##### 관리 콘솔
 * action에서 Create Change Set을 선택해서 변경점을 확인 가능
 * 변경 확인 화면에서 바로 Execute 버튼을 클릭해서 변경 가능


##### CLI

 * Stack 생성

```
$ aws cloudformation create-stack --stack-name example --template-body file://templates/single_instance.yml --parameters file://parameters/single_instance.json
```

 * create the change set

```
$ aws cloudformation create-change-set --stack-name example --template-body file://templates/instance_and_route53.yml --parameters file://parameters/instance_and_route53.json --change-set-name changeset-1
```

 * review change set

```
$ aws cloudformation describe-change-set --stack-name example --change-set-name changeset-1 | jq '.Changes[]'

{
  "ResourceChange": {
    "Action": "Add",
    "ResourceType": "AWS::Route53::RecordSet",
    "Scope": [],
    "Details": [],
    "LogicalResourceId": "DnsRecord"
  },
  "Type": "Resource"
}
```



 * change set 적용 

```
$ aws cloudformation execute-change-set --stack-name example --change-set-name changeset-1
```

# Tools

### jq

 * 컬러풀하게 표현

```shell
cat autoscaling.json | jq '.'
```

 * 최상위 section 출력

```
$ cat autoscaling.json | jq 'keys[]'
```

 * Show the leaf paths of the template structure

```
$ cat autoscaling.json | jq --compact-output '. | leaf_paths' | sed -e 's/,[0-9][0-9]*,/,1000,/g' | sed -e 's/,[0-9][0-9]*\]/,1000\]/g' | sort -u
```

 * Check out the Resources only

```
$ cat autoscaling.json | jq '.Resources'
```

 * jq to_entries 옵션
   * jq has a useful to_entries command that we can use to restructure the format to make it easier to traverse.

```
cat autoscaling.json | jq '.Resources | to_entries'
```

 * resource type 을 간추려 출력

```
$ cat autoscaling.json | jq '.Resources | to_entries | .[].value.Type'
```

 * sorting

```
$ cat autoscaling.json | jq -r '.Resources | to_entries | .[].value.Type' | sort | uniq -c | sort -r
   2 AWS::CloudWatch::Alarm
   2 AWS::AutoScaling::ScalingPolicy
   1 AWS::SNS::Topic
   1 AWS::ElasticLoadBalancingV2::TargetGroup
   1 AWS::ElasticLoadBalancingV2::LoadBalancer
   1 AWS::ElasticLoadBalancingV2::Listener
   1 AWS::EC2::SecurityGroup
   1 AWS::AutoScaling::LaunchConfiguration
   1 AWS::AutoScaling::AutoScalingGroup
```

##### Parameters

 * list the required parameters

```
$ cat single-instance.json | jq -r '.Parameters | to_entries | .[] | select(.value.Default == null) | .key'
KeyName
```

 * list the optional parameters

```
$ cat single-instance.json | jq -r '.Parameters | to_entries | .[] | select(.value.Default != null) | .key'
InstanceType
SSHLocation
```


### JSON to YAML

```shell
$ mkdir templates
$ cd templates
$ curl -o single_instance.json "https://s3-us-west-2.amazonaws.com/cloudformation-templates-us-west-2/EC2InstanceWithSecurityGroupSample.template"
$ ruby -ryaml -rjson -e 'puts YAML.dump(JSON.load(ARGF))' < single_instance.json > single_instance.yml
```


 * jq로 top level key 확인하기

```
$ cat single_instance.json | jq -r 'keys[]'
```

### YAML to JSON

```
ruby -ryaml -rjson -e 'puts YAML.dump(JSON.load(ARGF))' < example.yml > example.json
```