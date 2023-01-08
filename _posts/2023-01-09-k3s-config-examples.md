---
layout: post
title: K3s yaml examples
date: 2023-01-09 10:16 +1100
categories: [HomeLab, K3s]
tags: [homelab, k3s, k8s, documentation, kubectl]
---

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
