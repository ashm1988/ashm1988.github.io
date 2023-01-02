---
layout: post
title: k3s Documentation
date: 2022-10-22 13:36 +1100
categories: [HomeLab, k3s]
tags: [homelab, k3s, documentation, kubectl]
---

K3s is a fully compliant Kubernetes distribution with the following enhancements
(https://docs.k3s.io/)
YouTube tutorial (https://www.youtube.com/watch?v=1hwGdey7iUU&list=WL&index=1)

# Installation 
## Master Node 
- Run the below command to install the master node 
```bash
curl -sfL https://get.k3s.io | sh -s - --disable traefik --write-kubeconfig-mode 644 --node-name k3s-01-mgt
```

## Worker Nodes
- Run the below command to install the worker node, take note of the k3s token and server url. 
> The token is stored `/var/lib/rancher/k3s/server/node-token` on the master node
```bash
curl -sfL https://get.k3s.io | K3S_NODE_NAME=k3s-01-app01 K3S_URL=https://192.168.1.107:6443 K3S_TOKEN=mynodetoken sh -
```

## Nginx Ingress Controller
- Run the below command to install nginx ingress controller
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```

# Commands
### Kubectl get commands
```zsh
Examples:
  # List all pods in ps output format
  kubectl get pods
  
  # List all pods in ps output format with more information (such as node name)
  kubectl get pods -o wide
  
  # List a single replication controller with specified NAME in ps output format
  kubectl get replicationcontroller web
  
  # List deployments in JSON output format, in the "v1" version of the "apps" API group
  kubectl get deployments.v1.apps -o json
  
  # List a single pod in JSON output format
  kubectl get -o json pod web-pod-13je7
  
  # List a pod identified by type and name specified in "pod.yaml" in JSON output format
  kubectl get -f pod.yaml -o json
  
  # List resources from a directory with kustomization.yaml - e.g. dir/kustomization.yaml
  kubectl get -k dir/
  
  # Return only the phase value of the specified pod
  kubectl get -o template pod/web-pod-13je7 --template={{.status.phase}}
  
  # List resource information in custom columns
  kubectl get pod test-pod -o custom-columns=CONTAINER:.spec.containers[0].name,IMAGE:.spec.containers[0].image
  
  # List all replication controllers and services together in ps output format
  kubectl get rc,services
  
  # List one or more resources by their type and names
  kubectl get rc/web service/frontend pods/web-pod-13je7
  
  # List status subresource for a single pod.
  kubectl get pod web-pod-13je7 --subresource status
```
