---
layout: default
title: Beyond network
description: 
nav_order: 5
parent: Network
---

# VPC endpoint
[Access an AWS service using an interface VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#create-interface-endpoint)

The routing to the service is handled by AWS itself. When you create an endpoint, AWS will create a new route in the route table associated with your subnet. This route will direct the traffic to the service via the endpoint.  

So, even if you don't see a specific routing configuration in your VPC for the endpoint, AWS is handling that routing for you behind the scenes.


# Trouble Shooting
### EC2
* How does ARP works in AWS network.
  + https://www.youtube.com/playlist?list=PLg5wDGiV3XZ4EDZJecktPsPReieyHqg1i

* Checking network connections with arp and ip neigh
  + https://www.networkworld.com/article/969445/checking-network-connections-with-arp-and-ip-neigh.html

