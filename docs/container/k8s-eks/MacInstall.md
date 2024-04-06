---
layout: default
title: Mac
nav_order: 3
parent: EKS
---

mac에서 쿠버네티스 + EKS 관련 개발환경을 구축하는 내용을 정리함.

 * aws workshop ( https://archive.eksworkshop.com/ ) 을 참조함
   + 하지만, 이 워크샵의 경우 리눅스 환경용이기 때문에, 꼭 똑같이 할 수는 없음.
   + mac의 경우 zsh를 사용하는데, 리눅스에서 bash쉡을 사용하는 형태로 사용하고 있음.
   + 또 환경변수를 많이 설정하는데, 그런 부분은 스킵해도 크게 문제는 없다.


---
# kubectl 설치
쿠버네티스 버전에 맞는 kubectl 버전을 설치

* 최신 release된 k8s 다운로드
```
#curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
```

* 특정 버전의 release를 다운받으려면 $(curl -L -s https://dl.k8s.io/release/stable.txt) 대신 버전을 입력합니다.
```
#curl -LO "https://dl.k8s.io/release/v1.21.0/bin/darwin/amd64/kubectl"
```

### 바이너리 검증

* kubectl 체크섬 파일을 다운로드합니다.
```
#curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl.sha256"
```


* kubectl 바이너리를 체크섬 파일을 통해 검증합니다.
```
#echo "$(<kubectl.sha256)  kubectl" | shasum -a 256 --check
```

* kubectl 바이너리를 실행 가능하게 합니다.
```
#chmod +x ./kubectl
```

* kubectl 바이너리를 시스템 PATH의 파일 위치로 옮깁니다.
```
#sudo mv ./kubectl /usr/local/bin/kubectl && \
sudo chown root: /usr/local/bin/kubectl
```

* 설치한 버전이 최신 버전인지 확인합니다.
```
#kubectl version --client
```


---
# helm 설치

```
brew install helm

helm repo add stable https://charts.helm.sh/stable
helm repo add bitnami https://charts.bitnami.com/bitnami

helm search repo nginx

helm search repo bitnami/nginx

helm install mywebserver bitnami/nginx

kubectl get service mywebserver-nginx -o wide <= 접속 정보 찾기
```

helm은 kubectl처럼 버전에 민감하진 않나?


---
# eksctl 설치
eksctl 은 최신버전 설치해도 문제 없는 듯


---
# AWSCLI 설치
v2로 설치


---
# 환경 설정

개발PC에서 바로 EKS를 이용할 수 있도록 설정함.



### Update the kubeconfig file to interact with you cluster
```
aws eks update-kubeconfig --name eksworkshop-eksctl --region ${AWS_REGION}
```

### Export the Worker Role Name for use throughout the workshop:
```
STACK_NAME=$(eksctl get nodegroup --cluster eksworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.zshrc
```

### AM Users and Roles are bound to an EKS Kubernetes cluster via a ConfigMap named aws-auth. We can use eksctl to do this with one command.
```
eksctl create iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::507114771640:role/eksworkshop-admin --group system:masters --username admin
2023-02-24 23:07:16 [ℹ]  checking arn arn:aws:iam::507114771640:role/eksworkshop-admin against entries in the auth ConfigMap
2023-02-24 23:07:16 [ℹ]  adding identity "arn:aws:iam::507114771640:role/eksworkshop-admin" to auth ConfigMap
```

### you can verify your entry in the AWS auth map within the console.
```
kubectl describe configmap -n kube-system aws-auth  
```


---
# Kubernetes Dashboard

deploy the dashboard with the following command:
```
export DASHBOARD_VERSION="v2.6.0"

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml


(삭제시는 아래 명령으로)
# kill proxy
pkill -f 'kubectl proxy --port=8080'

# delete dashboard
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml
```

Since this is deployed to our private cluster, we need to access it via a proxy. kube-proxy is available to proxy our requests to the dashboard service. In your workspace, run the following command:
```
kubectl proxy --port=8080 --address=0.0.0.0 --disable-filter=true &
```
This will start the proxy, listen on port 8080, listen on all interfaces, and will disable the filtering of non-localhost requests.


URL에 아래 정보 추가하기(의미는?)
```
/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

토큰 조회
```
aws eks get-token --cluster-name eksworkshop-eksctl | jq -r '.status.token'
```
