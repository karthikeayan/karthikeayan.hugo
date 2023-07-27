---
title: aws-cloudwatch-k8s-api-log-filter
description: Filter EKS API Server Logs in Cloudwatch
tags: ["kubernetes", "eks", "logs", "query", "filter"]
date: 2023-07-27

---

# Filter EKS API Server Logs in Cloudwatch

Kubernetes Version: 1.25
Cloud Provider: AWS EKS

- Open Cloudwatch
- Open Logs Insights
- Select EKS API Server Log group

```
fields @timestamp, @message, sourceIPs.0
| filter responseObject.metadata.name like /REPLACE_NAMEPSACE_NAME_HERE/
| filter verb like /create/
| filter requestURI like /api\/v1\/namespaces/
| filter responseObject.kind like /Namespace/
| sort @timestamp desc
```
