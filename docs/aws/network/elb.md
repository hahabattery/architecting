---
layout: default
title: Elastic Load Balancer
description: 
nav_order: 5
parent: Network
---

describe knowledges about elb.



# Tip. How to healthcheck target port

following commands are applied to ECS task, but it can be applied other case, too.

* (Option 1) For HTTP health checks:

```
$ curl -Iv http://<example-task-private-ip>:<example-port>/<healthcheck_path>
```
Example output:

```
HTTP/1.1 200 OK
Note: You can receive successful status codes in the range of 200-399for configurations set for HTTP health checks on the target group.
```

* (Option 2) For TCP health checks that don't use SSL with the targets:

```
$ nc -z -v -w10 example-task-private-ip example-port
```
Example output:

```
nc -z -v -w10 10.x.x.x 80
Connection to 10.x.x.x port 80 [tcp/http] succeeded!
```

* (Option 3) For TCP health checks that require SSL for backend health checks:

```
$ nc -z -v -w10 --ssl example-task-private-ip example-port
```
Example output:

```
nc -z -v -w10 10.x.x.x 443
Connection to 10.x.x.x port 443 [tcp/https] succeeded!
```


