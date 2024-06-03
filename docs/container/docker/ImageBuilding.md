---
layout: default
title: Image Building
grand_parent: Container
parent: Docker
nav_order: 4
---



# multi-arch
https://github.com/docker/docs/blob/2d8b420d3c49712ec4a7bcec1464278fa4c41936/docker-for-mac/multi-arch.md

* 기존에는 실제 도커로 동작시키려고 하는  CPU 아키텍쳐에 해당하는 머신을 준비했어야 했으나,
* 최근에는 buildx라는 툴로는 크로스 이미지 빌딩이 되는 걸로 보임.

* [Multi-arch build and images, the simple way](https://www.docker.com/blog/multi-arch-build-and-images-the-simple-way/)

---
# Getting started with Docker for Arm on Linux
* https://www.docker.com/blog/getting-started-with-docker-for-arm-on-linux/

docker buildx 명령으로 멀티플랫폼에서 동작하는 이미지를 생성한다.

* multi-arch image를 확인하는 명령
```
docker manifest inspect --verbose arm64v8/alpine
```

---
# (udemy) I wonder the details of the specific modifications for Apple silicon chip.
* 요약
  + 동작시킬려고 하는 머신과 같은 CPU 아키텍져를 가진 PC를 준비한다
  + base image를 동작시킬려고 하는 CPU 아키텍쳐에서 동작하는 base 이미지를 준비한다.

A. Hi, two things needed to build an arm64 image

1. need for building the images on an arm64 machine like a Mac M1 or M2 - I used a Mac M2.
2. the base image when you run the docker build will be different.

For example, if you look at the Dockerfile for the position-tracker, the first line is:

```
FROM arm64v8/amazoncorretto:17.0.5-al2022-RC-headless
```

So I'm using an arm64 image of amazoncorretto, which is a Java image, so I don't have to install any java tools on to that.

(The full software is at https://github.com/DickChesterwood/k8s-fleetman/tree/master/microservice-source-code)

That's about it really. From here the images are built as normal.

And for building on Intel or AMD, use the same image tag but just the plain amazoncorretto image.

Intel images will usually run on Apple but it will use an emulation layer which we discovered runs too slowly for this system, hence the specially built images.

In theory I should build some windows images, but most windows developers run kubernetes and docker under virtualization somehow, so it generally isn't necessary. Introduction of Apple silicon was a real headache for the course!
