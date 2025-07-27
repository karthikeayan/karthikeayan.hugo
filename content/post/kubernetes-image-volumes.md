---
title: kubernetes-image-volumes
description: Kuberetes Image Volumes Feature in 1.33 (beta)
tags: ["kubernetes", "image", "volumes", "1.33", "dynamic"]
date: 2025-07-28

---

# Kuberetes Image Volumes Feature in 1.33 (beta)

Create kind cluster
```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
kubeadmConfigPatches:
  - |
    kind: ClusterConfiguration
    apiServer:
      extraArgs:
        feature-gates: "ImageVolume=true"
    controllerManager:
      extraArgs:
        feature-gates: "ImageVolume=true"
    scheduler:
      extraArgs:
        feature-gates: "ImageVolume=true"
  - |
    kind: KubeletConfiguration
    featureGates:
      ImageVolume: true
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30000
        hostPort: 30000
    kubeadmConfigPatches:
      - |
        kind: InitConfiguration
        nodeRegistration:
          kubeletExtraArgs:
            feature-gates: "ImageVolume=true"
```

```
kind create cluster --config kind-imagevolume.yaml
```

Once the cluster is up, create a pod with multiple image references as volumes,
```
# build-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: build-pod
spec:
  containers:
  - name: shell
    command: ["sleep", "infinity"]
    image: debian
    volumeMounts:
    - name: jdk21
      mountPath: /jdk21
      subPath: dir
    - name: python313
      mountPath: /python313
      subPath: dir
    - name: node24
      mountPath: /node24
      subPath: dir
  volumes:
  - name: jdk21
    image:
      reference: openjdk:21
      pullPolicy: IfNotPresent
  - name: python313
    image:
      reference: python:3.13
      pullPolicy: IfNotPresent
  - name: node24
    image:
      reference: node:24
      pullPolicy: IfNotPresent
```

```
kubectl apply -f build-pod.yaml
```

Here are the important lines,
```
  - name: jdk21
    image:
      reference: openjdk:21
      pullPolicy: IfNotPresent
```

We are attaching a volume but it is not a PVC or a ConfigMap or Secret. It is a container image itself.

Lets get into the image and see how it looks,
```
k exec -it build-pod -- bash
```

```
root@build-pod:/# df -h
overlay        1007G  7.7G  948G   1% /jdk21
overlay        1007G  7.7G  948G   1% /python313
overlay        1007G  7.7G  948G   1% /node24
```
Great, we can see mounts as the name we specified in the volumeMount section

Lets see whats inside in one of the folder,
```
root@build-pod:~# cd /jdk21/
root@build-pod:/jdk21# ls -l
total 60
lrwxrwxrwx 1 root root    7 Oct  9  2021 bin -> usr/bin
dr-xr-xr-x 2 root root 4096 Oct  9  2021 boot
drwxr-xr-x 2 root root 4096 Oct  9  2021 dev
drwxr-xr-x 1 root root 4096 Sep 21  2023 etc
drwxr-xr-x 2 root root 4096 Oct  9  2021 home
lrwxrwxrwx 1 root root    7 Oct  9  2021 lib -> usr/lib
lrwxrwxrwx 1 root root    9 Oct  9  2021 lib64 -> usr/lib64
drwxr-xr-x 2 root root 4096 Oct  9  2021 media
drwxr-xr-x 2 root root 4096 Oct  9  2021 mnt
drwxr-xr-x 2 root root 4096 Oct  9  2021 opt
dr-xr-xr-x 2 root root 4096 Sep 21  2023 proc
dr-xr-x--- 1 root root 4096 Sep 21  2023 root
drwxr-xr-x 4 root root 4096 Sep 21  2023 run
lrwxrwxrwx 1 root root    8 Oct  9  2021 sbin -> usr/sbin
drwxr-xr-x 2 root root 4096 Oct  9  2021 srv
dr-xr-xr-x 2 root root 4096 Sep 21  2023 sys
drwxrwxrwt 1 root root 4096 Sep 21  2023 tmp
drwxr-xr-x 1 root root 4096 Sep 21  2023 usr
drwxr-xr-x 1 root root 4096 Sep 21  2023 var
```
Cool, we can see the entire filesystem of the jdk21 image.

Lets try and see if we can really use it
```
root@build-pod:/jdk21# /jdk21/usr/java/openjdk-21/bin/java -version
openjdk version "21" 2023-09-19
OpenJDK Runtime Environment (build 21+35-2513)
OpenJDK 64-Bit Server VM (build 21+35-2513, mixed mode, sharing)
```
We can directly use java here. Lets see if we can use node and python

```
root@build-pod:/node24# /node24/usr/local/bin/node --version
v24.4.1

root@build-pod:/# /python313/usr/bin/python3 --version
/python313/usr/bin/python3: error while loading shared libraries: libexpat.so.1: cannot open shared object file: No such file or directory
```

node is working but python isn't. Looks like we need to set LD_LIBRARY_PATH for python to work,
```
root@build-pod:/node24# export LD_LIBRARY_PATH=/python313/usr/lib:/python313/usr/lib/x86_64-linux-gnu
root@build-pod:/node24# /python313/usr/bin/python3 --version
Python 3.11.2
```

Thats great. We can directly attach the images as volume and use it with this new feature.
No more 15GB or 30GB bulk images and no more struggle of maintaining the Dockerfiles.
