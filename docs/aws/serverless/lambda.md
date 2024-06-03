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

### Opertaion for Python
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


