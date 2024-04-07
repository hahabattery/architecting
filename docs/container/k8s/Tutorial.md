---
layout: default
title: Tutorial
nav_order: 3
parent: Kubernetes
grand_parent: Container
---

# 기본적인 개념

**개념 이해를 위한 검색 키워드**

 * cordon,drain,PDB
 * helm
 * pause container


![](/images/container/k8s/kubernetes-tutorial-01.png)
![](/images/container/k8s/kubernetes-tutorial-02.png)
![](/images/container/k8s/kubernetes-tutorial-03.png)
![](/images/container/k8s/kubernetes-tutorial-04.png)
![](/images/container/k8s/kubernetes-tutorial-04-1.png)
![](/images/container/k8s/kubernetes-tutorial-05.png)
![](/images/container/k8s/kubernetes-tutorial-06.png)
![](/images/container/k8s/kubernetes-tutorial-07.png)
![](/images/container/k8s/kubernetes-tutorial-08.png)
![](/images/container/k8s/kubernetes-tutorial-09.png)

### kubernetes에서 리소를 관리하는 방식
* Name과 Kind가 동일하면 같은 오브젝트로 본다.
  + 오브젝트를 변경하고 싶으면, Name과 Kind로 찾아서 변경하면 된다.


### 자주 사용하는 명령

* kubectl apply -f ...yaml
  + 설정 적용하기
* kubectl get services or pods or deployments
  + 상태 확인하기
  + ex) kubectl get pods -o wide  # <= 이 명령으로 pod가 어느 노드에서 동작하는지 알 수 있다.
  + ex) kubectl get pv or pvc  <= persistent volume (claim) 정보 조회
* kubectl delete <object_type> <object_name>
  + pod 삭제하기
  + ex) kubectl delete pod pod명
  + ex) kubectl delete service service명
* kubectl delete -f ...yaml
  + Object 삭제하기
* kubectl set image <object_type>/<object_name> <container_name> = <new iamge to use>
  + **Imperative**(delarative와 반대말) command (ugh) to update image
  + ex) kubectl set image deployment/client-deployment client=closeyes2/multi-client:v5
* kubectl describe <object type> <object name>
  + 상세정보 확인하기
  + ex) kubectl describe pod client-pod  <= client-pod라는 이름을 가진 pod 타입 오브젝트의 상세 정보를 조회
* kubectl create secret <type of secret> <secret_name> --from-literal PGPASSWORD=password123
  + secret 생성하기
  + kubectl create secret eneric pgpassword --from-literal PGPASSWORD=password123
* kubectl get storageclass
  + kubernetes가 컴퓨터에서 제공할 수 있는 옵션을 확인
* kubectl describe storageclass
  + storageclass 관련 더 자세한 정보를 확인
