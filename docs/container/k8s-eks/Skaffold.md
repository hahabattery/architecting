---
layout: default
title: Skaffold
nav_order: 3
parent: EKS
---


https://ashishtechmill.com/cicd-workflow-for-spring-boot-application-on-kubernetes-via-skaffold







[이해하기 쉽게 정리된 글](https://blog.slashuniverse.com/25)


# 설치
```
# For Mac
$ brew install skaffold

# For Windows
$ choco install -y skaffold
```

# 자주 쓰는 명령

```
skaffold build
```

# skaffold.yaml
[skaffold.yaml 파일의 모든 옵션이 정리된 레퍼런스](https://skaffold.dev/docs/references/yaml/)

이미지를 빌드하는 방법, 테스트하는 방법, 배포하는 방법까지 모두 이 파일에 작성

### build 섹션
이미지를 어떻게 빌드할 것인지, 태깅을 어떻게 할 것인지 등, 이미지를 빌드하고 푸시하는 것에 관한 설정이 담겨있다.

build.tagPolicy 속성을 통해 이미지를 빌드할 때 어떤 태그를 사용할지 명시할 수 있다. 아래는 태깅할 때 Git의 태그나 커밋 해시를 사용하는 예시이다. 이외에도 날짜와 시간을 태그로 사용하는 dateTime, 환경 변수를 사용하는 envTemplate 등이 있다.

```
build:
  tagPolicy:
    gitCommit: {}
```

만약 두 가지 형식을 모두 사용하고 싶다면 어떻게 해야 할까? 이럴 때는 customTemplate 옵션을 사용하면 된다. 아래는 현재 내가 사용하고 있는 태그 정책이다.
```
build:
  tagPolicy:
    customTemplate:
      template: "{{.DATETIME}}-{{.COMMITHASH}}"
      components:
        - name: DATETIME
          dateTime:
            format: "2006-01-02"
            timezone: "UTC"
        - name: COMMITHASH
          gitCommit:
            variant: AbbrevCommitSha
```
components에 정의된 요소들의 이름을 customTemplate.template에서 사용하고 있는 것을 확인할 수 있다. 이 템플릿 형식은 [golang template](https://pkg.go.dev/text/template) 을 따른다. <= Go를 만들고 그 Go로 Skaffold를 만들고 Go의 템플릿을 사용하는 구글 



이미지를 가지고 파드, 디플로이먼트같은 리소스를 배포하는 것에 관한 설정이 담겨있다.


 * Container Regstry설정
```xml
<registry>/<image-name>:<tag>
```
이미지를 푸시할 때는 위와 같이 저장소의 URI와 이미지의 이름을 합쳐 푸시를 하게 되는데, Skaffold에서는 이를 위해 Image Repository Handling 기능을 제공한다.


```command
# Using --default-repo flag
$ skaffold dev --default-repo <myrepo>

# Using SKAFFOLD_DEFAULT_REPO environmental variable
$ SKAFFOLD_DEFAULT_REPO=<myrepo> skaffold dev

# Using global config
$ skaffold config set default-repo <myrepo>
```
사용하는 저장소가 하나라면 세 번째, default-repo의 전역 설정을 이용하는 방법이 가장 간편할 것 같고, 혹시나 여러 저장소를 사용한다면 플래그를 사용하는 방식이 아무래도 편하지 않을까 싶다.


### deploy 섹션

kubectl, Helm 등의 배포 수단을 선택할 수 있는데, 여기서는 kubectl을 사용했다.
```
deploy:
  kubectl:
    defaultNamespace: bser-stage
    manifests:
      - k8s/dev-*.yaml
```

위의 manifests 옵션에서 배포를 위해 사용할 manifest 파일을 지정하고 있다. 이 매니페스트에는 여러 가지 리소스들이 있을텐데, 그중 Deployment의 파드 스펙에 api-dev 라는 이미지를 사용했다고 하자.
```
spec:
  containers:
    - name: er-api-node
      image: api-dev
      imagePullPolicy: IfNotPresent
```

앞에 Skaffold 설정에 이미지 이름을 api-dev로 해두었다. 이렇게 이미지 이름이 일치할 경우 배포를 할 때 api-dev라는 이미지는 실제 빌드된 이미지로 치환되어(이름이 바뀌어?) 배포된다.
```
build:
  artifacts:
    - image: api-dev
      context: .
      docker:
        dockerfile: Dockerfile
```


skaffold run 명령으로 배포 이후 Deployment의 이미지 스펙을 확인해봤다. 이미지 레지스트리와 태그가 정상적으로 달린 것을 볼 수 있다.
```
❯ kubectl get deploy er-api-dev --template="{{ (index .spec.template.spec.containers 0).image }}"
552543234276.dkr.ecr.ap-northeast-2.amazonaws.com/bser/api-dev:2021-08-03-8dff6d7-dirty@sha256:8ca0d8b78b49fd5fd63c9ea118803c6eb08f5af46060901c7822e56de7a5d5df
```

### profiles 섹션
워크플로우를 실행할 때 프로필에 따라 다른 기능을 수행할 수 있도록 설정할 수 있다.

처음 서비스를 만들 때는 테스트와 운영 환경을 동일하게 가져가는 경우가 많은데, 서비스의 규모가 커지고 별도의 테스트 환경의 필요성이 생기면 테스트 용도와 운영 용도의 환경을 분리하게 된다.

이런 환경을 보통 스테이징 환경이라고 하고, 필요에 따라 두 개, 세 개 이상으로 환경이 늘어날 수 있다.

환경이 달라지는 경우 배포하는 코드 역시 달라지기 마련이다. 스테이징 서버에는 내부 테스트를 위한 코드가 배포될 것이고, 운영 서버에는 모든 테스트가 끝난 코드가 배포될 것이다. 이 말은 즉 각각의 환경마다 배포하는 이미지가 달라져야 한다는 것.

이 문제는 어떻게 해결할 수 있을까? Skaffold에서는 프로필을 통해서 이를 지원한다.
 
```
profiles:
  - name: stage
    deploy:
      kubectl:
        defaultNamespace: bser-stage
        manifests:
          - k8s/dev-*.yaml
```

프로필에서 정의하는 build, deploy, test 섹션은, 해당 프로필로 Skaffold 워크플로우를 실행했을 때 기존의 각 섹션을 대체하게 된다. 원래 정의되어있던 값이 무엇이든 관계없이, 새로운 값들로 대체되는 것이다.


몇 번 테스트와 구글링을 거치면 프로필마다 어느 부분이 바뀌는지 깔끔하게 정리가 된다. 이렇게 만들어진 프로필은 skaffold run 혹은 skaffold dev 명령을 실행할 때 플래그로 지정할 수 있다.

```
skaffold run -p stage
```


