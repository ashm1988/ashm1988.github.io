---
layout: post
title: k3s Documentation
date: 2022-10-22 13:36 +1100
categories: [HomeLab, k3s, k8s]
tags: [homelab, k3s, k8s, documentation, kubectl]
---

K3s is a fully compliant Kubernetes distribution with the following enhancements
(https://docs.k3s.io/)
YouTube tutorial (https://www.youtube.com/watch?v=1hwGdey7iUU&list=WL&index=1)

# Installation 
## Master Node 
Run the below command to install the master node 
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

# Config file examples
## Deployment yml example
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongoexpress-deployment
  labels:
    # needs to match the service selector app
    app: mongoexpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongoexpress
  template:
    metadata:
      labels:
        app: mongoexpress
    # Pod specific
    spec:
      containers:
      - name: mongoexpress
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        # secret example
        - name: ME_CONFIG_BASICAUTH_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-user
        # configmap example  
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom:
            configMapKeyRef:
              name: mongo-config
              key: mongo-url
```

## Service yml example
```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    # selector app needs to match the deployment label app & pod label app
    app: nginx
  ports:
    - protocol: TCP
      # external port to hit
      port: 8080
      # port to hit within the container
      targetPort: 80
```

## Service example with node port
Node port lets you connect to he pod from externally, node port has to be in the range `default: 30000-32767`
```yml
apiVersion: v1
kind: Service
metadata:
  name: mongoexpress-service
spec:
  type: NodePort
  selector:
    app: mongoexpress
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30081
```

## Configmap example
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-config
data:
  # to get the service url use the below
  # servicename.namespace.svc.cluster.local
  mongo-url: mongo-service.default.svc.cluster.local
```

## Secretmap example
Values need to be base64 encoded. To encode use `echo -n <value> | base64`
```yml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
data:
  mongo-user: YXNo
  mongo-password: R2VvcmdlcXcxMg==
```

## Ingress example
```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mongoexpress-ingress
spec:
  # ingressClassName is necessary since v1.22 
  ingressClassName: nginx
  rules:
  - host: kubehost.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mongoexpress-service
            port:
              number: 8081
```

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