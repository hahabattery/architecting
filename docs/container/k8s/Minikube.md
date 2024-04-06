---
layout: default
title: Minikube
nav_order: 10
parent: Kubernetes
---

minikube  관련 내용,

최근에는 테스트용도로는 Docker Desktop을 많이 사용하는 거 같다.

 * https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide
   + <= 요 강의에 minikube관련 내용이 들어있었던 거 같다.

# 리소스
* [설치 관련 공식 매뉴얼](https://kubernetes.io/docs/setup/)
  * 실환경과 Learning Environment을 나눠서 가이드하고 있다.

---
# 설치 순서

### Virtual Machine 을 설치하는 경우
> Minikube usually uses a Virtual Machine image which contains a full Kubernetes distribution. This works quite well, but as you'll see, it can be a pain to set up. You don't need to install Docker to use minikube, as minikube ships with its own Docker installation inside it.

이 설치 옵션의 경우에 minikube 가 Virtual Machine에서 동작하기 때문에
아래 명령으로 접근할 IP를 찾아야 한다.
```
minikube ip
```

### docker가 설치된 경우
To use the **docker driver**
1. install minikube , ensure Docker is running
2. Now start minikube with the command "minikube start --memory 4096 --driver docker". Actually, you don't need the --driver argument, because the docker driver becomes the default when you have Docker running. But let's include it to remind ourselves what is happening.
3. Now minikube should start up in the usual way


### Docker Desktop이 설치된 경우
Alternative: if you have **Docker Desktop**, things are even easier...
1. Go straight to the video called "Installing kubectl and minikube", but you don't need to install minikube!
2. in Docker Dekstop settings, "Kubernetes" entry tick "Enable Kubernetes"
3. All Docker Desktop is doing is running minikube in the background.

---
# Docker Driver and Ingress - IMPORTANT

We've noticed many macOS and Windows students attempting to take the course with the docker driver when using Minikube instead of Docker Desktop.

The docker driver is not supported for use in this course if you are on macOS or Windows. It currently does not work with any type of ingress:

https://minikube.sigs.k8s.io/docs/drivers/docker/#known-issues

If you have started the course with the docker driver, you will need to switch to a different driver in order to continue.

Delete your cluster:
```
minikube delete
```
Restart with a different driver:

### macOS:
```
minikube start --driver=hyperkit
```
or
```
minikube start --driver=virtualbox
```
### Windows:

Windows students should be using Docker Desktop with WSL2 and not Minikube. A VM driver will not work since it would require virtualization that is in conflict with WSL2. You should stop and head back to the instructions to enable Docker Desktop's Kubernetes instead.

Linux:

If you are a Linux user, the ingress add-on should be supported when using the docker driver.

* 결론
  + minikube 이용시에는 ingress 테스트하기가 까다로운거 같다.
---
# Tip

### Important note for Docker Desktop and Driver Users (네트워크 관련해서)

* docker driver 이용한 설정시
If you're using the Docker Driver, you will need to issue a command to access your service. Using "minikube ip" won't work!


* docker desktop 이용 설정시
If you are using the Kubernetes that ships with Docker, then you just use "localhost" or "127.0.0.1" as the address. Don't forget the port number of your NodePort. so "127.0.0.1:30080" should work for the webapp.

For some users, localhost doesn't work and you will need the following command:
```
kubectl port-forward webapp 30080:80
```

이렇게 네트워크 설정을 하고 나서, docker driver를 이용한 경우에는 아래 명령으로 실행이 가능하다!
```
minikube service fleetman-webapp
```
This will return some output like this:


This should automatically open the application in a browser tab. If not, the address that you need to access in your browser is given at the bottom of the output (after the "starting tunnel" announcement) - in this example, it will be "http://127.0.0.1:56064". The port number (56064 in this case) is generated randomly so make a careful note of it.

You now need to leave this process running - you will need to continue your work in another terminal/command prompt.
