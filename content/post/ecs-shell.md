---
title: ecs-shell
description: get-shell-of-a-container-running-on-ECS
tags: ["aws", "ecs", "shell", "bash", "sh"]
date: 2021-12-12
publishdate: 2021-12-12
---

# Get shell of a container running on ECS

ECS is a managed container orchestration service by AWS. Known for its simplicity, especially ECS Fargate.

Since ECS Fargate nodes are completely managed by AWS, there is no way to SSH into the node.

Also ECS was lacking a command line option just like,

```
kubectl exec
```

On March 2021, AWS launched a feature which is similar to "kubectl exec" to obtain a shell access of a container running on ECS Fargate.

<https://aws.amazon.com/blogs/containers/new-using-amazon-ecs-exec-access-your-containers-fargate-ec2/>

This helped me a lot to troubleshoot issues.

Since I have to login into lots of different containers running on different ECS Clusters which in turn running on different AWS Accounts, it became difficult to copy paste and edit the command everytime.

So, I just wrote a very simple shell script which can help me. Here is the script.

```
#!/usr/local/bin/bash

echo Enter Cluster Name:
read -e cluster_name

echo Enter Container Name:
read -e container_name

echo Enter Service Name:
read -e service_name

echo Enter AWS Profile Name:
read -e profile

region="eu-west-1"

task_ids=($(aws ecs list-tasks --cluster ${cluster_name} --service-name ${service_name} --profile ${profile} --region ${region} | jq '.taskArns[]' -r | cut -d'/' -f3))

counter=1
declare -A tasks
for i in "${task_ids[@]}"
do
  echo "${counter}: ${i}"
  tasks["${counter}"]="${i}"
  counter=$((counter+1))
done

echo Enter Task Number:
read task_counter

aws ecs execute-command --cluster ${cluster_name} --task ${tasks["${task_counter}"]} --container ${container_name} --interactive --command "/bin/sh" --profile ${profile} --region ${region}
```
