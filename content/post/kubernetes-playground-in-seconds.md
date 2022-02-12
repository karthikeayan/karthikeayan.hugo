---
title: kubernetes-playground-in-seconds
description: get-kubernetes-playground-in-seconds
tags: ["kubernetes", "playground", "sandbox", "practice"]
date: 2022-02-12
publishdate: 2022-02-12
---

# Get kubernetes cluster in seconds to play with

Go to
```
https://labs.play-with-k8s.com/
```

Login and execute below command on instance1
```
kubeadm init --apiserver-advertise-address $(hostname -i)
```

Copy kubeadm join command from the kubeadm init output and execute join in instance2
```
kubeadm join --token SOMETOKEN SOMEIPADDRESS --discovery-token-ca-cert-hash SOMESHAHASH

kubectl apply -n kube-system -f \
    "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 |tr -d '\n')"
```
