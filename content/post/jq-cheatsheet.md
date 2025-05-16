---
title: jq-cheatsheet
description: JQ CheatSheet
tags: ["jq", "cheatsheet", "cli", "json"]
date: 2021-10-01
publishdate: 2021-10-01
---

# JQ CheatSheet

If condition
```
aws apigateway get-rest-apis --profile prod | jq '.items[] | select(.name == "My Secure API") | .id' -r
```

Multiple if condition
```
aws s3api get-object-tagging --bucket my-bucket --key screenshot.png | jq '.TagSet[] | select(.Key == "dev" and .Value == "true")'
```

If condition, print parent element
```
aws apigateway get-rest-apis --profile prod | jq '.items[] | . as $item | select(.name == "My Secure API") | $item'
```

If condition, print parent element with contains
```
aws elbv2 describe-load-balancers | jq --arg lbname \"${lb_name}\" '.LoadBalancers[] | select( (.LoadBalancerName | contains($lbname)) and .Scheme == "internal" ) | .DNSName' -r
```

Pass argument to jq from Jenkins sh step
```
aws appconfig list-configuration-profiles --application-id ${appId} | jq --arg rds \"${rds_name}\" '.Items[] | select(.Name == $rds) | .Id' -r
```

Find all the deployment names in Kubernetes, which contains environment variable value from from Kubernetes secret with specific key
```
k get deploy  -o json \
  | jq '.items[] | . as $item
  | .spec.template.spec.containers[].env | select (. != null)
  | .[].valueFrom | select (. != null)
  | .secretKeyRef | select (. != null)
  | select (.key == "YOUR_KUBERNETES_SECRET_KEY") | $item
  | .metadata.name' -r
```
