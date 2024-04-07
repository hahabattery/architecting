---
layout: default
title: Trouble Shooting
nav_order: 3
parent: Kubernetes
grand_parent: Container
---



# The connection to the server localhost:8080 was refused - did you specify the right host or port?

보통은 kube-apiserver 서버가 동작하지 않아서 발생하는 경우가 많다.

초기 설정 이슈이다보니 Clean uninstall하는게 나을 것이다.

https://blog.hkwon.me/macoseseo-docker-clean-uninstall-bangbeob/


# pod 상태가 ImagePullBackOff 이다

kubectl describe pod {podid} <= 문제가 되는 pod의 상세 내용을 확인한다.

실서버에서 이런 일이 발생하는 경우에는 근본적인 원인을 해결하는(도커 이미지 이름이 잘못됨) 것은 시간이 많이 소요되는 경우가 많기 때문에,

아래 내용을 참조해서 이전 버전으로 되돌리는 것이 확실한 해결책이다.

```
kubectl rollout history deployment webapp  <= deployment 히스토리를 확인한다.

kubectl rollout undo deploy webapp --to-revision=2     <= 배포를 히스토리의 REVISION으로 되돌린다.
```

# pod가 계속해서 재시닥될 때
로그를 확인할 수 있다.
```
kubectl logs {podid}
```

pod의 설정및 이벤트를 확인
```
kubectl describe pod {podid}
```

# 메모리가 부족할 때
메모리 사용량을 지정해서 쾌적하게 테스트할 수 있다.

* docker driver 이용하는 경우인 듯..(docker driver를 안써봐서...)
I recommend before starting this section that you set up minikube with plenty of RAM. To do this:

1. Stop minikube with "minikube stop"
2. Delete your existing minikube with "minikube delete"
3. Remove any config files with "rm -rf ~/.kube" and "rm -rf ~/.minikube". Or, delete these folders using your file explorer. The folders are stored in your home directory, which under windows will be "c:\Users\<your username>"
4. Now restart minikube with "minikube start --memory 4096".


 * Docker Desktop
go to Preferences-->Resources, increase the memory to 4GB
