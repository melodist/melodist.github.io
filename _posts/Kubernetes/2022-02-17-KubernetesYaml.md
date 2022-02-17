---
layout: post
title: Kubernetes Yaml 정리
tags: [Kubernetes]
permalink: docs/Kubernetes/Yaml
date: 2021-02-17 23:37:00
---

# Kubernetes 리소스 관리

- 모든 리소스는 `apiVersion`, `kind`, 그리고 `metadata` 필드를 가짐

### Deployment

- [파드](https://kubernetes.io/ko/docs/concepts/workloads/pods/)와 [레플리카셋(ReplicaSet)](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset/)에 대한 선언적 업데이트를 제공

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```

### Service

- [파드](https://kubernetes.io/ko/docs/concepts/workloads/pods/) 집합에서 실행중인 애플리케이션을 네트워크 서비스로 노출하는 추상화 방법

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

### Ingress

- 클러스터 외부에서 클러스터 내부 [서비스](https://kubernetes.io/docs/concepts/services-networking/service/)로 HTTP와 HTTPS 경로를 노출

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```

### References

https://kubernetes.io/ko/docs/
