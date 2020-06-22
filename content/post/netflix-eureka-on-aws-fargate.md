---
title: netflix-eureka-on-aws-fargate
description: Deploying Netflix Eureka on AWS Fargate
tags: ["aws", "fargate", "service-discovery", "netflix-eureka"]
---

# Configuration for deploying Netflix Eureka in AWS Fargate

## What is Netflix Eureka?
Service Discovery service from Netflix.

## Why this article?
Service Discovery can be achieved with [ECS Service Discovery](https://aws.amazon.com/blogs/aws/amazon-ecs-service-discovery/) feature. However I have asked to work on resolve the issues while deploying Eureka into AWS Fargate. I ran into multiple challenges, sharing the knowledge what I gained here.

## Architecture
![Architecture](/static/images/netflix-eureka-on-fargate.png)

## Steps

* Create 3 different spring profiles to run in 3 different Availability Zones
* Specify other Availability Zone details in the eureka.client.serviceUrl.defaultZone
* For example, in Zone A profile, http://b.eureka.server:9992/eureka,http://c.eureka.server:9993/eureka
* Create 3 ECS Service with task count 1, 1 service for each Availability Zone
* Create 1 Network Load Balancer in AWS
* Create 4 Target Groups
    * One for Availability Zone A on port 9991
    * One for Availability Zone B on port 9992
    * One for Availability Zone C on port 9993
    * One for all the Availability Zones on port 80
* Create 4 listeners in NLB and attach the target groups accordingly
* Listener 80 will be used by the microservices
* Listener starts with 99 will be used by the Eureka server itself for the replication
* Make sure you have enabled the Cross Zone Load Balancing in NLB
* Add eureka.instance.hostname to the spring profile
    * For A profile --> a.eureka.server
    * For B profile --> b.eureka.server
    * For C profile --> c.eureka.server
* Create Route53 private zone DNS entry for each of the replicas but all 3 will be pointing to the NLB Hostname
* Set preferIpAddress to false in the spring profile

## Dockerfile

Fargate container IP address will not be the same as the Network Interface IP Address which gets attached to the Task. So we need to tweak the Dockerfile to get the Network Interface IP address and use it in Eureka.

```
FROM openjdk:8-jdk-alpine

RUN apk add --no-cache curl
RUN apk add --no-cache jq

WORKDIR /app
COPY target/eureka-server*.jar /app/eureka-server.jar
COPY src/main/resources/config/application*.* /app/
COPY docker-entrypoint.sh /app/

RUN chmod +x /app/docker-entrypoint.sh
ENTRYPOINT ["/app/docker-entrypoint.sh"]
```

### docker-entrypoint.sh
```
#!/bin/sh

export ECS_INSTANCE_IP_ADDRESS=$(/usr/bin/curl --retry 5 --connect-timeout 3 -s http://169.254.170.2/v2/metadata | /usr/bin/jq .Containers[0].Networks[0].IPv4Addresses[0] -r)

exec java ${JAVA_OPTS} -Deureka.instance.ip-address=${ECS_INSTANCE_IP_ADDRESS} -Djava.security.egd=file:/dev/./urandom -jar /app/eureka-server.jar "$@"
```