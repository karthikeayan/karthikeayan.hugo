---
title: nerdctl-docker-alternate
description: Nerdctl, Docker Alternate
tags: ["docker", "alternate", "opensource", "free", "macos", "lima", "nerdctl"]
date: 2021-11-13
publishdate: 2021-11-13
---

# Nerdctl, Docker Alternate

Using Docker in local desktop is not free anymore for large businesses,
```
Our Docker Subscription Service Agreement includes a change to the terms for Docker Desktop
It remains free for small businesses (fewer than 250 employees AND less than $10 million in annual revenue), personal use, education, and non-commercial open source projects.
```
Extracted from, https://www.docker.com/products/docker-desktop

Many of us using Docker Desktop in day to day activities are part of large businesses. So I tried searching for an alternate and found this.
```
https://github.com/containerd/nerdctl
```

nerdctl is Docker compatible CLI for Containerd.

What is Containerd?
It is a container runtime.

Who uses Containerd?
Docker :blush:

So what is the difference between Docker and Containerd?
At a high level, Docker providers rich user interface for users to work with containers.

Okay, now any other alternateives for Docker?
Yes. There are few. Even, there is a blog in Docker website about this.
```
https://www.docker.com/blog/looking-for-a-docker-alternative-consider-this/
```

Okay, which one works?
For me, nerdctl.

I tried podman. But I ran into this issue, https://github.com/containers/podman/issues/11396

I tried nerdctl, couldn't face any issues so far.

How to run nerdctl in MacOS?
Inside Virtual Machine.

Is there any CLI/Tools which can simply this process?
Yes, Lima.
```
https://github.com/lima-vm/lima
```

How to start?
```
brew install lima
limactl start
```

Once you execute above commands, you can see an output where it says, READY.

Then you can start using nerdctl, like below:
```
lima nerdctl run -d --name nginx -p 127.0.0.1:8080:80 nginx:alpine
```

Till I get used to the new command, I thought, I will use an alias in my ~/.zshrc file pointing "docker" to "lima nerdctl" 
:smile: :smile: :smile:
