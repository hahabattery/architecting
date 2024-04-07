---
layout: default
title: Overview
nav_order: 1
parent: Kubernetes
grand_parent: Container
---


---

# 쿠버네티스

**yaml파일이 쿠버네티스 이용할 때 까다로운거 같다. 접할 때마다, 해당 내용을 검색해보기.**

udemy의 skaffold강좌 수강하기

https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide

위 강의의 18강에 설정 내용이 있음.

udemy의 lstio 부분 수강하기

https://www.udemy.com/course/master-microservices-with-spring-docker-kubernetes/ ← 맨 후반부 강의

Docker and Kubernetes: The Complete Guide https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide **← 이 강좌 보다맘 12강부터 보면 될듯.**

https://github.com/techiescamp/kubernetes-learning-path

### udemy강좌리스트 **← 다시 안봐도 되게 정리해놓기…(1/5 진행중)**

https://www.udemy.com/course/master-microservices-with-spring-docker-kubernetes/

https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/

https://www.udemy.com/course/docker-kubernetes-the-practical-guide/

https://www.udemy.com/course/docker-mastery/

https://www.udemy.com/course/kubernetes-microservices/  **← ELK적용하는 부분에서 중지함. 다시 시작할 떄는 여기서부터 하자.**


---

kubernetes를 테스트하면서, 환경을 맞추는 것이 어려웠다.

kubectl이나 eksctl등을 설치할 때, 환경(OS, CPU 종류)에 맞춰서 설치를 해야지 테스트가 가능하기 때문에, 개별 패키지 설치 방법을 환경에 맞춰서 정리해야된다.

Cloud9은 워크샵 실습하는데 까진 괜찮은데, 실제 소스 수정을 하면서 진행하기에는 Cloud9에서 IntelliJ와 같은 IDE설치가 안되기 때문에, 거추장 스럽게 된다.

결론은 소스 개발하는 환경에 쿠버네티스 배포까지 되도록 환경을 맞춰야 한다.

현재 여러가지로 테스트를 해보았는데, 개발환경에 쿠버네티스를 바로 동작시키는 게 원활하지 않아서 아래 형태로 환경을 구성하고 있다.



 * 개발환경
   + IntelliJ
   + Docker Desktop   docker-compose로 테스트환경까지 생성해보는 수준
   + kuberctl, eksctl EKS를 대상으로 조작
   + skaffold         EKS를 대상으로 테스트

 * EKS
   + 쿠버네티스가 동작하는 환경은 비용이 발생하지만, 클라우드벤더를 이용하고 있다.

 * 20231007
   + 메모리 64G 머신(맥프로)에서 로컬에서 쿠버네티스 동작시키는 것을 다시 테스트중.
   + kubernetes가 버전별로 설정방법이 바뀐다든가 하는게 많아서 까다로운 부분이 많은거 같다.
   + 그중에서 가장 까다로운 부분은 yaml같은 이미 작성되있는 설정이 오래된 경우에 kubectl 버전을 맞춰야만, 제대로 동작하는 경우가 있다는 것이다(deprecation되는 API들이 있어서...).
   + 만약에 kubectl 버전이 더 최신인 경우(자주 이런 상황에 놓이게 됨)에는 yaml파일을 최신버전의 kubectl에 맞춰서 수정해줘야 한다.
   + 테스트결과) 딱히 on premise에서 동작시킬 생각은 없지만, 문제는 안된다.

---
# 리소스
 * [A roadmap to learn Kubernetes from scratch (Beginner to Advanced level)](https://github.com/techiescamp/kubernetes-learning-path)

### 워크샵
* EKS 워크샵
  + https://awskocaptain.gitbook.io/aws-builders-eks/

* EKS 워크샵(종류많음)
  + https://www.eksworkshop.com/
  + https://archive.eksworkshop.com <- 이건 옛날 버전인데, 전에 자주 참조했던 것, 있다는 것만 알아두자.


---
# 설치
* [설치 관련 공식 매뉴얼](https://kubernetes.io/docs/setup/)
  * 실환경과 실습환경을 나눠서 가이드하고 있다.


docker desktop 으로 하면 매우 빠르게 로컬 테스트환경을 구축할 수 있다.


---


---
# 네임스페이스
논리적으로 분리된 자원의 영역.
```
kubectl get namespaces     <= 네임스페이스 목록

kubectl get pods -n kube-system  <= 특정 네임스페이스를 지정해서 할당된 pods를 조회한다.
```

지정되지 않으면 'default' 네임스페이스로 할당된다.



---
# 네트워크

pod는  쿠버네티스 클러스터 안에서 접근가능한 private IP를 가진다.

이 아이피는 쿠버네티스에 의해서 동적으로 할당되므로, pod이름(호스트명)으로 접근하게 도와주는 discovery 서비스가 필요하다.

이 떄문에 kube-dns라는 private DNS 서비스를 가지고 있다.

이 서비스는 kube-system 라는 네임스페이스안에 존재한다.

### FQDN
Fully Qualified Domain Names

컨테이너에 보면 DNS 설정이 들어있음.
```
# cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local  <= 왼쪽부터 먼저 찾아지는 걸 사용함.
options ndots:5
```

---
# Ingress

* Setup of ingress-nginx changes depending on your environment (local, GC, AWS, Azure)

### ingress-nginx
* https://github.com/kubernetes/ingress-nginx
* a community led project
* https://www.joyfulbikeshedding.com/blog/2018-03-26-studying-the-kubernetes-ingress-system.html
  + Just in case you wanted to understand ingress-nginx a bit better, check out this article by Hongli Lai


### kubernetes-ingress
* https://github.com/nginxinc/kubernetes-ingress
* a project led by the company nginx

### Ingress-Nginx VS Nginx Ingress
* Ingress-Nginx Controller
  + https://kubernetes.github.io/ingress-nginx/
* Nginx Ingress Controller
  + https://docs.nginx.com/nginx-ingress-controller/

### Ingress-Nginx Installation
* Installation - Docker Desktop (macOS and Windows)
  + https://kubernetes.github.io/ingress-nginx/deploy/#quick-start
  ![](/images/container/k8s/ingress-01.png)


* Installation - Minikube
  + https://kubernetes.github.io/ingress-nginx/deploy/#minikube
  ![](/images/container/k8s/ingress-02.png)

설치후에 LoadBalance IP 가 가능해진지를 확인하는 명령.
```
kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller
```

---
# Persistence
pod가 재기동되어도 과거 데이터를 남기기 위해서

* https://kubernetes.io/docs/concepts/storage/persistent-volumes/
* https://kubernetes.io/docs/concepts/storage/storage-classes/

* 검색키워드
  + "emptyDir"
  + "hostPath"
  + "persistentVolume"
  + "persistentVolumeClaim"

* emptyDir
  + emptryDir은 Pod가 사라지면 볼륨도 같이 삭제되는 임시 볼륨의 성격을 가지고 있고 Pod가 실행되는 디스크의 공간에  볼륨 마운트를 하게 됩니다. 위의 이유로 Life cycle이 컨테이너가 아닌, Pod 단위로 되어있어서 컨테이너가 어떠한 문제로 삭제되어도 Pod는 실행중이므로 데이터는 emptyDir에 의해 보관되며 Pod가 삭제되는 순간 emptyDir로 보관중이던 모든 데이터는 삭제 됩니다.
* hostPath
  + hostPath는 노드의 디스크에 볼륨을 생성하여 Pod가 삭제 되더라도 볼륨에 있던 데이터는 유지 됩니다.
* persistentVolume (물리적인 스토리지) and persistentVolumeClaim (pod가 실제로 요청하는 volume정보)
  + PV는 관리자에 의해 생성된 볼륨을 뜻하고, PVC는 사용자가 볼륨을 사용하기 위해 PV에 요청을 한다.
  1. 프로지버지닝provisioning  <= 정적또는 동적의 pv를 생성하는 단계
  2. 바이딩binding <= PV를 PVC에 연결시키는 단계
  3. 사용using  <= pod는 PVC를 볼륨으로 사용한다. 클러스터는 PVC를 확인해서 바인딩된 PV를 찾고 해당 볼륨을 pod에서 사용할 수 있도록 해준다.
  4. 회수reclaming <= PV는 기존에 사용했던 PVC가 아니더라도 다른 PVC로 재활용 할 수도 있다.
    + Retain : PV의 데이터를 그대로 보존
    + Recycle : 재사용하게될 경우 기존의 PV 데이터들을 모두 삭제 후 재사용
    + Delete : 사용이 종료되면 해당 볼륨을 삭제

### Access Modes
* ReadWriteOnce
  + Can be used by a single node.
* ReadOnlyMany
  + Multiple nodes can read from this
* ReadWriteMany
  + Can be read and written to by many nodes



### Tip
persistent volume을 잘 사용하고 있는지 확인하는 방법은 단순하게 pod를 아래갘은 명령으로 지우는 것이다.
```
kubectl delete pod/mongodb-sldfjskdff3902
```
그러면 deployment 처리에 의해서 자동으로 새로운 pod가 기동된다. 그러면서 데이터가 날라가지 않고, 기존 데이터를 잘 조회하고 있는지 확인하면 된다.


---
# yaml파일 작성법
매뉴얼에 상세 스펙이 나와 있다.

예를 들어서 pods를 위한 yaml파일의 상세 스펙은 다음과 같다.

https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/

yaml파일하나에 여러가지 kind (Pod, Service, ReplicaSet, Deployment)를 입력해도 됨. 따로 제한이 있지는 않음.

* yaml파일의 template 항목은 생성되는 리소스에 포함되는 설정이다.

### spec.type
ClusterIP, NodePort, LoadBalance


---
### Volume (docker VS kubernetes)


일반적으로 docker container에서 volume은 Some type of mechanism that allows a container to access a filesystem outside itself를 의미한다.

하지만

**쿠버네티스에서 volume은 An object that allows a container to store data at the pod level 를 의미한다.**

### Kubernetes Volume


---
# 보안

### 네트워크 정책
쿠버네티스 클러스터에 접속하는 것을 통제하는데, pod간 통신또는 다른 네트워크 엔드포인트와 pod사이의 통신을 제어하는 기능.

네트워크 정책을 사용하면 IP주소, 포트, 프로토콜및 레이블(label)과 같은 기준으로 트래픽을 허용/거부 시킬 수 있음.

또는, 특정 네임스페이스로부터 들어오는 트래픽만 허용하는 네트워크 정책을 세울 수도 있다.

네트워크 정책을 설정하는 기능은 플러그인에 의존한다.
* Calico
* Cilium
* Weave Net


### RBAC(역활 기반 접근 제어)
RBAC는 사용자및 서비스 계정의 역할을 정의하고, 역할에 따라 권한을 부여해 클러스터 리소스에 대한 접근을 제어합니다.
사용자나 서비스 계정의 역할을 설정하는 Role, 클러스터 수준에서 역할을 설정하는 ClusterRole을 사용해 보안 규칙을 정의하고, 이 규칙을 사용자의 모임인 그룹(Group)에 추가한다.

* Role : Role은 권한의 집합으로, 특정 네임스페이스에 한정된 리소스에 대한 권한을 부여한다.
* ClusterRole : 클러스터 전체에 한정된 정책
* RoleBinding : 특정 사용자 또는 서비스 계정에 Role을 할당하는 것.(어떤 역할이 특정 네임스페이스에서 하는가?)
* ClusterRoleBinding : 특정 사용자 또는 서비스 계정에 ClusterRole을 할당.
