---
title: java-dynamic-heap-size
description: Java Dynamic Heap Size
tags: ["java", "heap", "memory", "oom", "dynamic"]
date: 2021-05-31
publishdate: 2021-01-31
---

# Java Dynamic Heap Size

Below statistics are tested on: amazoncorretto:8-alpine-jre

### Percentage values can be used to set Maximum Heap Size:

```
java -XX:+HeapDumpOnOutOfMemoryError -XX:OnError='echo JVM exited with error' \
  -XX:MaxRAMPercentage=90.0 \
  -XX:MinRAMPercentage=40.0 \
  -XX:+PrintGCDetails -jar app.jar
```

When using percentage java heap size is set as below,

| Container Memory    | Heap Size      |
| --------------------|:--------------:|
| 8G                  | 6.40G          |
| 16G                 | 12.80G         |
| 32G                 | 25.60G         |
| 64G                 | 26.67G         |
| 128G                | 26.67G         |

So, JVM Heap Size doesn't go beyond 26.67G when using XX:MaxRAMPercentage


### Use static value to set Maximum Heap Size when container memory is greater than 32G

```
java -XX:+HeapDumpOnOutOfMemoryError -XX:OnError='echo JVM exited with error' \
    -Xmx=64G \
    -XX:+PrintGCDetails -jar app.jar
```
