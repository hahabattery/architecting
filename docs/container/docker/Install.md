---
layout: default
title: Docker Setup
grand_parent: Container
parent: Docker
nav_order: 4
---

docker 를 설치하는 방법을 정리

# Amazon Linux 2

install docker
```
sudo yum update
sudo yum search docker
sudo yum info docker
sudo yum install docker

sudo usermod -a -G docker ec2-user
id ec2-user # Reload a Linux user's group assignments to docker w/o logout
newgrp docker
```

install docker-compose
```
# 1. Get pip3
sudo yum install python3-pip

# 2. Then run any one of the following
sudo pip3 install docker-compose # with root access

# OR #

pip3 install --user docker-compose # without root access for security reasons
```

system setting
```
# Enable docker service at AMI boot time:
sudo systemctl enable docker.service

# Start the Docker service:
sudo systemctl start docker.service

# Get the docker service status on your AMI instance, run:
sudo systemctl status docker.service
```
