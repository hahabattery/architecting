---
layout: default
title: Trouble Shooting
grand_parent: AWS
parent: Cloud Development Kit
nav_order: 5
---

# TSError: ⨯ Unable to compile TypeScript:

I had met same issue. First I remove ts-node and typescript from package.json. then,
```
npm install ts-node --save-dev
npm install typescript -g 
npm install typescript --save-dev
```

# unable to resolve aws account

https://docs.aws.amazon.com/cli/latest/userguide/sso-configure-profile-token.html

sso 설정을 따로 해줘야 한다. 그러면 왜 AWS Toolkit for Visual Studio를 쓰나...흠..
```
aws configure sso
```


