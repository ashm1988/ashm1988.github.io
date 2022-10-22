---
layout: post
title: k3s Documentation
date: 2022-10-22 13:36 +1100
categories: [HomeLab, k3s]
tags: [homelab, k3s, documentation]
---

K3s is a fully compliant Kubernetes distribution with the following enhancements
(https://docs.k3s.io/)

# Installation 
## Master Node 
- Run the below command to install the master node 
```bash
curl -sfL https://get.k3s.io | sh -
```

## Worker Nodes
- Run the below command to install the worker node, take note of the k3s token and server url. 
> The token is stored `/var/lib/rancher/k3s/server/node-token` on the master node
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

# Commands
- Show nodes 
```zsh
sudo k3s kubectl get node
```