---
layout: post
title: K3s Documentation
date: 2022-10-22 13:36 +1100
categories: [HomeLab, K3s]
tags: [homelab, k3s, k8s, documentation, kubectl]
---
# Description
K3s is a fully compliant Kubernetes distribution with the following enhancements
<https://docs.k3s.io/>  
YouTube tutorial <https://www.youtube.com/watch?v=1hwGdey7iUU&list=WL&index=1>

# Installation 
## Master Node 
Run the below command to install the master node 
> Note: Installing version `INSTALL_K3S_VERSION=v1.24.9+k3s1` as there seems to be compatibility issuses with the latest and Rancher UI install via helm

```bash
# --disable traefik - Running with nginx ingress controller  
# --disable servicelb - Running with MetelLB loadbalancer 
# INSTALL_K3S_VERSION=v1.24.9+k3s1 at the time of writing, installing version 1.24.9 as rancherui/helm has compatibility issues with the latest version
curl -sfL https://get.k3s.io |INSTALL_K3S_VERSION=v1.24.9+k3s1 sh -s - --disable traefik --disable servicelb --write-kubeconfig-mode 644 --node-name k3s-01-mgt
```

## Worker Nodes
Run the below command to install the worker node, take note of the k3s token and server url. 
> The token is stored `/var/lib/rancher/k3s/server/node-token` on the master node  

```bash
curl -sfL https://get.k3s.io |INSTALL_K3S_VERSION=v1.24.9+k3s1 K3S_NODE_NAME=k3s-01-app01 K3S_URL=https://192.168.1.107:6443 K3S_TOKEN=mynodetoken sh -
```

## Nginx Ingress Controller
Run the below command to install nginx ingress controller  

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```

## Kubectl install on local machine
Follow the [Kubectl install Doco site](https://kubernetes.io/docs/tasks/tools/) to install locally. Then get the kubectl config from the server with `kc config view --raw`. Make a `~/.kube/config` file and paste the contents, updating the server ip to the cluster master.
> Note: Kubectl is installed on the Cluster manager as part of the install package.


## Helm install
[Helm install Doco site](https://helm.sh/docs/intro/install/)
```zsh
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```