---
layout: default
title: Overview
nav_order: 3
parent: EKS
grand_parent: Container
---






# 리소스
 * [Deploy Amazon RDS databases for applications in Kubernetes](https://aws.amazon.com/ko/blogs/database/deploy-amazon-rds-databases-for-applications-in-kubernetes/)
   + -> 쿠버네티스에서 db를 기동시킬때 컨테이로 띄워서 쓰는곳은 보통 없다. 별도의 구동 시스템을 사용한다. (참고로 GCP의 경우에는 gcloud SQL이라고 하는 매니저먼트 서비스에서 MySQL, Oracle, PostgreSQL을 사용할 수 있다.) AWS는 RDS를 사용할 것이다.

 * [Deploy PostgreSQL, MySQL, MariaDB Instances Using the ACK RDS Controller](https://aws-controllers-k8s.github.io/community/docs/tutorials/rds-example/#install-the-ack-service-controller-for-rds)
   + -> db를 kubernetes에 설치하는 경우의 가이드

또한, ACK라고 하는 AWS Controllers for Kubernetes를 이용해서 k8s가 RDS인스턴스에 바로 접근할 수 있도록 한다.
> The ACK service controller for Amazon Relational Database Service (RDS) lets you manage RDS database instances directly from Kubernetes. This includes the following database engines:


### EKS workshop

 * https://archive.eksworkshop.com/  <= 쿠버네티스 v1.21 버전 워크샵
 * https://www.eksworkshop.com/   <= 최신!


# EKS
kubernettes fargate를 사용할 수 있게금 2018년 업데이트 됨.

upstream kubernetes를 이용해서 (소스 수정없이) 서비스하고 있음.

onpremise의 구성 스크립트를 EKS에서 변경없이 사용가능함.

kubernettes는 선언형 ochestraction tool

사람이 수동으로 작업해서 서비스를 유지보수하지 않아도 된다.

코드로써 인프라를 관리할 수 있어서 (infrastructure as a code)

최소 6개의 EC2가 필요함.
마스터노드 3개, API서버 3개
스냅샷을 뜨면서, scaleout scaleup 관리를 AWS가 자동으로 해줌.

eksctl이 EKS의 공인 ctl이 되었음.

EKS 모니터링시에 cloud watch에서 container insight라는 view를 제공해준다고 하니, 활용하면 좋을 듯 하다.

### EKS의 특징

EKS는 3개의 마스터노드를 AZ를 다르게 해서 배포함
ETCD나 API같은 리소스를 자동으로 관리해준다.
2시간마다 스냅샷을 떠서 복원도 가능함.

멀티테넌스로 컨테이너를 배포하는 서비스

ECS <-> EKS 간의 변경은 containerized되었기 때문에, 간단하다.

![](/images/container/k8s-eks/start-1.png)
![](/images/container/k8s-eks/start-2.png)
![](/images/container/k8s-eks/start-3.png)
![](/images/container/k8s-eks/start-4.png)
![](/images/container/k8s-eks/start-5.png)
![](/images/container/k8s-eks/start-6.png)
![](/images/container/k8s-eks/start-7.png)

### EKS vs Kops

* Kops
  + Master Node를 직접 관리해야 한다(책임 증가).
  + 쉽고, 많이 사용되고 있음.
  + kops는 first party기 때문에, 빠른 지원이 보장된다.
* EKS
  + Master Node에 접근할 수 없다(신경을 덜 쓰게 됨).
  + eksctl 이라는 명령어는 third party 툴에 불과하다.
  + eksctl은 AWS에서만 사용된다(AWS 에 락인된다), 하지만 yaml파일은 다른 클라우드에서도 사용할 수 있다.
  + EKS는 fargate와 통홥되어있다. EKS실행 옵션에서 EC2와 fargate둘중에서 선택할 수 있다.

 * Kops 요금
   + 노드타입에 따라 요금이 나온다. m3.medium 600$/년
   + LoadBalancer 200$/년
   + 합계) 800$/년 for single master controle plane
   + 위 가격은 master node 한대일 떄 가격.
 * EKS 요금
   + set fee(설치요금?) for the entire control plane 876$/년 (2020년도 기준)
   + 위 가격에 multiple master node 도 포함됨.

---
# AWS에서만 사용되는 서비스
K8s를 AWS에서 동작시키기 위해 추가된 것들!

* ACK(AWS Controllers for Kubernetes)
  + https://aws-controllers-k8s.github.io/community/docs/tutorials/rds-example/#install-the-ack-service-controller-for-rds <- RDS를 연결할 때 이용 (생각보다 복잡하다)
  + helm chart에 "oci://public.ecr.aws/aws-controllers-k8s/rds-chart" 라는 이름형식으로 등록되어있다.
* AWS ALB Ingress Controller
  + ->  deprecated되었다. AWS Load Balancer Controller로 마이그레이션하자.
* AWS Load Balancer Controller
  + 외부 접근이 필요한경우에 사용 AWS ALB Ingress Controller의 new 버전

### AWS 에서만 사용되는 유틸
* AWS CLI
* eksctl


### k8s이용시에 AWS에서 사용되는 리소스들
* LoadBalancer
* Autoscaling Group

---
# 요금

 * EKS생성시 시간당 USD 0.10 = USD73/m
 * EKS로 만들어지는 모든 리소스(EC2, EBS 용량, 트래픽, ELB)에 대해서 비용발생


* 주의) 클러스터 삭제시에 volume 리소스가 남는 경우가 많아서, 꼭 확인해서 지우자.
  + gp2의 경우 0.1$/기가 이다.

---
# 네트워킹, 보안


![EKS Control Plane Deep Dive](/images/container/k8s-eks/EKSControlPlaneDeepDive.png)


![EKS Networking - VPC](/images/container/k8s-eks/EKSNetworking-VPC.png)


![EKS Networking - Security Groups](/images/container/k8s-eks/EKSNetworking-SecurityGroups.png)
* https://github.com/freach/kubernetes-security-best-practice/blob/master/README.md#firewall-ports-fire

![Security Groups Rules Visualized](/images/container/k8s-eks/SecurityGroupsRulesVisualized.png)

![EKS Pod Networking](/images/container/k8s-eks/EKSPodNetworking.png)
* https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html#AvailableIpPerENI
  + EC2인스턴스에 생성할 수 있는 Pod의 개수 = (instance type별로 가질수 있는 network interface X Private IPv4 addresses per interface)


![Network Security With Calico](/images/container/k8s-eks/NetworkSecurityWithCalico.png)


![Kubernets IAM And RBAC Integration](/images/container/k8s-eks/KubernetsIAMandRBACIntegration.png)
**인증(authentication)은 AWS IAM으로, 인가(authorization)는 K8s RBAC(알백)으로**


![Kubernets Worker Nodes](/images/container/k8s-eks/K8sWorkerNodes.png)
워커 노드에 IAM role 정보를 세팅한다.


![EKS Load Balancers](/images/container/k8s-eks/EKSLoadBalancers.png)


![EKS Load Balancers](/images/container/k8s-eks/EKSLoadBalancer-details.png)
* https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer


![Application LoadBalancer Ingress](/images/container/k8s-eks/ApplicationLoadBalancerIngress.png)
* https://github.com/kubernetes-sigs/aws-alb-ingress-controller


---
# Kubernetes dashboard

![Kubernetes Dashboard](/images/container/k8s-eks/KubenetesDashboard-01.png)

---
# HPA
Horizontal Pod Autoscaler


https://archive.eksworkshop.com/beginner/080_scaling/

kubectl autoscale 로 시작하면 HPA 에 대한 명령이고,

kubectl scale 로 시작하면 Cluster Autoscaler에 대한 명령이다.


---
# CA
Cluster Autoscaler


https://archive.eksworkshop.com/beginner/080_scaling/   <= IAM roles for service accounts에 대한 내용이 도 있으므로 참고할 것.
