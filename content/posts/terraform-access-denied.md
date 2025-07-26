---
title: terraform-access-denied-access-denied
description: Terraform Access Denied Access Denied
tags: ["aws", "terraform", "s3", "state"]
date: 2021-01-21
publishdate: 2021-01-21
---

# Terraform Access Denied Access Denied

You are working with Terraform, AWS and S3 for Remote State and suddenly you see a message
```
AccessDenied: Access Denied
```

Terraform doesn't give you lots of information, you are checking your IAM access, checking your Terraform provider and scratching your head what is going on.

This is what I did.
* I ran "terraform apply" with provider pointing to Account A.
* Terraform created few resources in AWS and its corresponding state in S3 bucket.
* I found, I ran "terraform apply" with the wrong provider.
* I corrected the typo in the provider to point to Account B and I ran "terraform apply" again.
* Now, I am running "terraform apply" with provider pointing to Account B.

This is what happened.
```
AccessDenied: Access Denied
```

This is why it happened.
Terraform looks for the remote state file in the S3 bucket and the provider is pointing to Account A. But in my code provider is pointing to Account B.

This is how it can be fixed.
* Point the provider to Account A.
* Run "terraform destroy" to completely destory everything which got created in Account A.
* Now point provider to Account B.
* Run "terraform apply"
* Go to sleep
