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

