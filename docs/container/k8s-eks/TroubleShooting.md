---
layout: default
title: Trouble Shooting
nav_order: 3
parent: EKS
grand_parent: Container
---



# EBS volume fails to create

클러스터를 지웠다가 다시 생성해준다.
```
eksctl delete cluster fleetman

eksctl create cluster --name fleetman --nodes-min=3
```


# storageclass.storage.k8s.io "cloud-ssd" not found

I got the same error I'm currently using cluster version 1.26.
There's a quick fix which I tried, as we need an ebs CSI driver we can install it using eks addon - https://docs.aws.amazon.com/eks/latest/userguide/managing-ebs-csi.html

Run these 2 below commands and this will install the add-on, then recreate the k8s manifests and it will work fine.


1. replace the cluster name with your cluster name below.
```
eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=fleetman --approve

eksctl create iamserviceaccount  --name ebs-csi-controller-sa --namespace kube-system   --cluster fleetman   --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy   --approve   --role-only   --role-name AmazonEKS_EBS_CSI_DriverRole   
```
2. Replace the "account_id" with the aws account ID of your account.
```
eksctl create addon --name aws-ebs-csi-driver --cluster fleetman --service-account-role-arn arn:aws:iam::ACCOUNT_ID:role/AmazonEKS_EBS_CSI_DriverRole --force
```


# Cannot create cluster 'fleetman' because us-east-1e, the targeted availability zone, does not currently have sufficient capacity to support the cluster
클러스터를 생성할 떄, 문제가 되는 AZ를 제거해서 다시 EKS 클러스터를 생성한다.

```
eksctl create cluster --name fleetman --nodes-min=3 --zones=us-east-1a,us-east-1b,us-east-1c,us-east-1d,us-east-1f
```

관련정보)  EBS CSI드라이버에서는 Amazon EKS 클러스터가 영구 볼륨을 위해 Amazon EBS 볼륨의 수명 주기를 관리할 수 있게 해주는 CSI 인터페이스를 제공한다.

# Your current IAM principal doesn’t have access to Kubernetes objects on this cluster
https://stackoverflow.com/questions/70787520/your-current-user-or-role-does-not-have-access-to-kubernetes-objects-on-this-eks

If you're logged in with the root user and get this error, run the below command to edit the aws-auth configMap:
```
kubectl edit configmap aws-auth -n kube-system
```

Then go down to mapUsers and add the following (replace [account_id] with your Account ID)
```
mapUsers: |
  - userarn: arn:aws:iam::[account_id]:root
    groups:
    - system:masters
```


# FailedScheduling ... volume node affinity conflict
https://stackoverflow.com/questions/51946393/kubernetes-pod-warning-1-nodes-had-volume-node-affinity-conflict

https://www.udemy.com/course/kubernetes-microservices/learn/lecture/11157538#questions/12523674

volume은 1b에 있었고, pod가 기동하는 EC2는 1a, 1c, 1f에 있어서 문제가 되는 상황이었다.
