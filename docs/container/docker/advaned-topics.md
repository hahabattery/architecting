---
layout: default
title: Advanced Topic
grand_parent: Container
parent: Docker
nav_order: 4
---

# Time

If there are no time zone settings, the Docker container operates based on UTC. Surprisingly, the host machine's time zone information is not applied.

### Environment variable
You can set a specific timezone while creating a Docker container by passing the desired timezone as an environment variable using the -e flag.

```
docker run -e TZ=Your/Timezone your_image
```

if you want to set the timezone to “America/New_York”

```
docker run -e TZ=America/New_York your_image 
```

### Changing the Timezone of an Existing Docker Container

* access to container using "docker exec"

```
docker exec -it container_id /bin/bash 
```

* install tz data

```
apt-get update && apt-get install -y tzdata 
```

* configure timezone

```
dpkg-reconfigure tzdata 
```

### Docker file

* Set TZ Environment variable

```
RUN apt-get update && apt-get install -y tzdata
ENV TZ=Your/Timezone
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
```