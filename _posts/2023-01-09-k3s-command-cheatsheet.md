---
layout: post
title: K3s command cheat sheet
date: 2023-01-09 10:19 +1100
categories: [HomeLab, K3s]
tags: [homelab, k3s, k8s, kubectl, cheatsheet]
---

# Commands
### Most Common Commands
```zsh
  # List all pods in ps output format
  kubectl get pods

  # Show and edit deployment config 
  kubectl edit deployment <deployment name>

  # Show logs 
  kubectl logs <pod name>

  # More details about the pod
  kubectl describe pod <pod name>

  # Scale one or multiple deployments
  kubectl scale --replicas=2 deployment nginx-depl mysql-depl

  # Scale all deployments
  kubectl scale --all --replicas=5 --namespace=default deployment

  # Connect to a pods shell 
  kubectl exec -it nginx-depl-5b59dcd777-qvzfr -- bin/bash

  # Delete deployment (and in turn replicaset)
  kubectl delete deployment <deployment name>
```