---
layout: default
title: Lambda
grand_parent: AWS
parent: Serverless
nav_order: 3
---


# Layer
* [Lambda Layer Overview](https://docs.aws.amazon.com/lambda/latest/dg/chapter-layers.html)
* [Working with layers for Python Lambda functions](https://docs.aws.amazon.com/lambda/latest/dg/python-layers.html)

* **Layer 생성시 Lambda의 runtime에 맞는 바이너리를 세팅하기 위해서(다운로드 받기위한) 환경을 세팅하는 것은 경우의 수가 많아서, 그때 그때 세팅을 맞추어야 한다.패키지 매너지의 도움을 받기 어려운 경우는 설치하려고 하는 패키지의 수동설치 가이드를 찾아서 설치해야한다.**


# Python 환경 구성
* 각 Lambda 런타임에 대한 계층 경로 (https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)
![](/aws/serverless/layer-paths.png)


```
mkdir basiclib
cd basiclib

mkdir -p python/lib/python3.12/site-packages/

pip3 install pymysql -t python/lib/python3.12/site-packages/
pip3 install pycryptodomex -t python/lib/python3.12/site-packages/

zip -r basiclib.zip * 
```

### Compatibility between local and lambda environment
패키지를 생성하는 시스템과 람다 환경이 호환이 되지 않을 수 있기 때문에 유의할 것.

호환이 되지 않는 라이브러리를 layer에 추가하는 경우에는 해당 환경을 도커를 이용해서 빌드할 수 있다.

> Lambda layers are just a way to add files to the lambda execution environment. All the files in the Layer (which is a zip file) are extracted to the /opt folder.

### invalid ELF header
docker를 이용하면 아래와 같이 처리 가능하다.

1. 모듈을 다운로드 받을 폴더를 생성
2. docker로 amazonlinux 컨테이너를 실행
3. 이때 컨테이너와 모듈을 다운로드 받을 폴더를 볼륨으로 연결!!
4. 컨테이너 쉘에서 모듈을 다운로드
5. 컨테이너에서 빠져나온 뒤에
6. 모듈을 zip 파일로 압축하기


```
1 -> mkdir lambda_layer
2,3 -> docker run -it --rm -v $(pwd)/lambda_layer/:/lambda_layer amazonlinux:2 bash
4 -> yum install -y python3 pip3
  -> cd /lambda_layer
  -> pip3 install pymysql pycryptodomex -t ./python
5 -> exit
6 -> zip -r pyjwt_layer.zip ./python (이때 pwd는 lambda_layer 폴더)
```

### install specific python version using package manager

```
sudo yum install -y amazon-linux-extras

amazon-linux-extras | grep python3.8

sudo amazon-linux-extras enable python3.8
```

### 수동 설치

아래 명령들은 python3.12 버전을 설치하는 내용이다. 설치방식이나 설치버전과 동작환경에 따라서 구체적인 실행명령은 달라질 수 있다. 

Update System
```
sudo yum update 
```

Install needed library
```
yum groupinstall 'Development Tools' 
yum erase openssl-devel -y
yum install openssl-devel bzip2-devel libffi-devel sqlite-devel 
yum install openssl11 openssl11-devel  libffi-devel bzip2-devel wget -y
```

Download
```
wget https://www.python.org/ftp/python/3.12.1/Python-3.12.1.tgz 
tar xzf Python-3.12.1.tgz 
```

Configure, Build, Install
```
cd Python-3.12.1 
./configure --enable-optimizations 

make

sudo make altinstall
```

PIP 환경 구성
```
python3.12 -m ensurepip --upgrade 
```
