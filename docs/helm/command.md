---
layout: post
title: "How-to-use-Helm"
parent: Helm
date: 2025-07-15
nav_order: 1
excerpt: Command of helm
---

## Helm grammer

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: { { .Release.namepsace } }
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
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

---

## Helm 주요 command

```bash
helm install --upgrade -n {namespace} {chart name} {directory} -f {file name} # If there is a helm chart, upgrade it, and if not, install it.

helm ls -n {namespace} # Check the status with the helm list

helm uninstall -n {namespace} {chart name} # Remove helm chart in namespace

helm template -n {namespace} {chart name} {directory} -f {value file} # Chech the chart grammer

helm install --dry-run -n {namespace} {chart name} {directory} -f {file name} # Run a mock helm install
```

---
