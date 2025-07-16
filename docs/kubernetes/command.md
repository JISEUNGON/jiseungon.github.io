---
layout: post
title: "How to use kubectl"
date: 2025-07-16
nav_order: 1
parent: Kubernetes
---

## 목차

- [What is kubectl?](#what-is-kubectl)
- [How to use kubectl](#how-to-use-kubectl)
  - [Useful setting](#useful-setting)
    - [자동완성 설정](#자동완성-설정)
    - [별칭(Alias) 설정](#별칭alias-설정)
    - [kubectl 플러그인 설치](#kubectl-플러그인-설치)
    - [환경 변수 설정](#환경-변수-설정)
    - [kubectl 설정 파일](#kubectl-설정-파일--kubeconfig)
    - [유용한 kubectl 플러그인들](#유용한-kubectl-플러그인들)
    - [프로덕션 환경 설정](#프로덕션-환경-설정)
  - [Primary command](#primary-command)
    - [Show resource list](#show-resource-list)
      - [기본 조회 명령어](#기본-조회-명령어)
      - [-o wide 옵션](#-o-wide-옵션)
      - [리소스 타입별 조회](#리소스-타입별-조회)
    - [Edit resource](#edit-resource)
    - [Scale resource](#scale-resource)
      - [스케일 가능한 리소스 종류](#스케일-가능한-리소스-종류)
      - [스케일링 명령어 예시](#스케일링-명령어-예시)
  - [Additional useful commands](#additional-useful-commands)
    - [리소스 상세 정보 조회](#리소스-상세-정보-조회)
    - [로그 조회](#로그-조회)
    - [파드 내부 접속](#파드-내부-접속)
    - [포트 포워딩](#포트-포워딩)
    - [리소스 삭제](#리소스-삭제)
    - [YAML 파일로 리소스 관리](#yaml-파일로-리소스-관리)
    - [네임스페이스 관리](#네임스페이스-관리)
    - [컨텍스트 관리](#컨텍스트-관리)
    - [디버깅 도구](#디버깅-도구)

---

# What is kubectl?

- kubectl은 Kubernetes 클러스터와 상호작용하기 위한 명령줄 도구입니다.
- 클러스터의 리소스를 관리하고, 애플리케이션을 배포하며, 문제를 진단하는 데 사용됩니다.

# How to use kubectl

## Useful setting

kubectl을 더 효율적으로 사용하기 위한 유용한 설정들입니다.

### 자동완성 설정

#### Bash 자동완성

```bash
# kubectl 자동완성 스크립트 추가
echo 'source <(kubectl completion bash)' >>~/.bashrc

# 현재 세션에 적용
source ~/.bashrc

# 또는 직접 실행
source <(kubectl completion bash)
```

### kubectl 설정 파일 (~/.kube/config)

```yaml
apiVersion: v1
kind: Config
clusters:
  - name: my-cluster
    cluster:
      server: https://kubernetes.example.com
      certificate-authority-data: <base64-encoded-ca-cert>
contexts:
  - name: my-context
    context:
      cluster: my-cluster
      user: my-user
      namespace: default
current-context: my-context
users:
  - name: my-user
    user:
      client-certificate-data: <base64-encoded-client-cert>
      client-key-data: <base64-encoded-client-key>
```

### 프로덕션 환경 설정

```bash
# 네임스페이스별 컨텍스트 설정
kubectl config set-context --current --namespace=production

# 리소스 제한 확인
kubectl top nodes
kubectl top pods

# 클러스터 상태 확인
kubectl get componentstatuses
kubectl get nodes -o wide
```

## Primary command

### Show resource list

- 리소스 목록을 조회하는 기본 명령어들입니다.

#### 기본 조회 명령어

```bash
# 모든 네임스페이스의 파드 조회
kubectl get pods --all-namespaces

# 특정 네임스페이스의 파드 조회
kubectl get pods -n default

# 모든 리소스 타입 조회
kubectl get all
```

#### -o wide 옵션

`-o wide` 옵션을 사용하면 더 자세한 정보를 볼 수 있습니다.

```bash
# 파드의 상세 정보 조회 (IP, 노드 정보 포함)
kubectl get pods -o wide

# 서비스의 상세 정보 조회 (클러스터 IP, 외부 IP 포함)
kubectl get services -o wide

# 디플로이먼트의 상세 정보 조회
kubectl get deployments -o wide
```

#### 리소스 타입별 조회

```bash
# 디플로이먼트 조회
kubectl get deploy

# 서비스 조회
# port 조회
kubectl get service

# StatefulSet 조회
kubectl get sts

# 파드 조회
kubectl get pod

# ConfigMap 조회
kubectl get configmap

# Secret 조회
kubectl get secret

# Role 조회
kubectl get role

# ServiceAccount 조회
kubectl get serviceaccount

# RoleBinding 조회
kubectl get rolebinding

# PersistentVolumeClaim 조회
kubectl get pvc

# PersistentVolume 조회
kubectl get pv
```

### Edit resource

리소스를 직접 편집할 수 있습니다.

```bash
# 디플로이먼트 편집
kubectl edit deployment my-app

# 서비스 편집
kubectl edit service my-service

# ConfigMap 편집
kubectl edit configmap my-config

# Secret 편집
kubectl edit secret my-secret
```

편집기가 열리면 YAML 형식으로 리소스를 수정할 수 있습니다. 저장하면 변경사항이 즉시 클러스터에 적용됩니다.

### Scale resource

리소스의 복제본 수를 조정할 수 있습니다.

#### 스케일 가능한 리소스 종류

- **Deployment**: 가장 일반적인 스케일링 대상
- **StatefulSet**: 상태를 가진 애플리케이션
- **ReplicaSet**: 디플로이먼트의 하위 리소스
- **HorizontalPodAutoscaler**: 자동 스케일링

#### 스케일링 명령어 예시

```bash
# 디플로이먼트 스케일링 (3개 복제본으로 확장)
kubectl scale deployment my-app --replicas=3

# StatefulSet 스케일링 (5개 복제본으로 확장)
kubectl scale statefulset my-stateful-app --replicas=5

# 현재 복제본 수 확인
kubectl get deployment my-app -o jsonpath='{.spec.replicas}'

# 실시간으로 복제본 수 모니터링
kubectl get pods -l app=my-app -w
```

## Additional useful commands

### 리소스 상세 정보 조회

```bash
# 파드의 상세 정보 조회
kubectl describe pod my-pod

# 디플로이먼트의 상세 정보 조회
kubectl describe deployment my-app

# 서비스의 상세 정보 조회
kubectl describe service my-service
```

### 로그 조회

```bash
# 파드 로그 조회
kubectl logs my-pod

# 실시간 로그 조회 (follow)
kubectl logs -f my-pod

# 이전 컨테이너 로그 조회
kubectl logs my-pod --previous

# 특정 컨테이너 로그 조회 (멀티 컨테이너 파드)
kubectl logs my-pod -c container-name
```

### 파드 내부 접속

```bash
# 파드 내부로 쉘 접속
kubectl exec -it my-pod -- /bin/bash

# 특정 컨테이너로 접속
kubectl exec -it my-pod -c container-name -- /bin/bash

# 명령어 실행
kubectl exec my-pod -- ls /app
```

### 포트 포워딩

```bash
# 로컬 포트를 파드 포트로 포워딩
kubectl port-forward my-pod 8080:80

# 서비스 포트 포워딩
kubectl port-forward service/my-service 8080:80
```

### 리소스 삭제

```bash
# 파드 삭제
kubectl delete pod my-pod

# 디플로이먼트 삭제
kubectl delete deployment my-app

# 네임스페이스 삭제 (모든 리소스 포함)
kubectl delete namespace my-namespace

# 라벨로 리소스 삭제
kubectl delete pods -l app=my-app
```

### YAML 파일로 리소스 관리

```bash
# YAML 파일로 리소스 생성
kubectl apply -f deployment.yaml

# 여러 파일 적용
kubectl apply -f k8s/

# 리소스 삭제
kubectl delete -f deployment.yaml

# YAML 형식으로 출력
kubectl get pod my-pod -o yaml
```

### 네임스페이스 관리

```bash
# 네임스페이스 생성
kubectl create namespace my-namespace

# 네임스페이스 목록 조회
kubectl get namespaces

# 특정 네임스페이스의 모든 리소스 조회
kubectl get all -n my-namespace
```

### 컨텍스트 관리

```bash
# 현재 컨텍스트 확인
kubectl config current-context

# 컨텍스트 목록 조회
kubectl config get-contexts

# 컨텍스트 변경
kubectl config use-context my-cluster

# 클러스터 목록 조회
kubectl config get-clusters
```

### 디버깅 도구

```bash
# 파드 이벤트 조회
kubectl get events --sort-by='.lastTimestamp'

# 노드 정보 조회
kubectl describe node my-node

# 클러스터 정보 조회
kubectl cluster-info

# API 리소스 목록 조회
kubectl api-resources
```

이러한 명령어들을 조합하여 Kubernetes 클러스터를 효과적으로 관리할 수 있습니다.
