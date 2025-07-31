---
title: "ClusterRoleBinding"
parent: Kubernetes Components
nav_order: 9
date: 2025-07-31
excerpt: ClusterRoleBinding component in Kubernetes
---

## ClusterRoleBinding 개요

ClusterRoleBinding은 Kubernetes에서 ClusterRole과 사용자/그룹/서비스 계정을 클러스터 전체 범위로 연결하는 리소스입니다. 모든 네임스페이스에 적용되는 권한을 관리합니다.

### ClusterRoleBinding의 특징

1. **클러스터 범위**: 모든 네임스페이스에 적용
2. **ClusterRole 연결**: ClusterRole만 바인딩 가능
3. **시스템 권한**: 클러스터 관리자 권한 할당
4. **전역 접근**: 모든 네임스페이스의 리소스에 접근

---

## ClusterRoleBinding 구조

```mermaid
graph TD
    A[ClusterRoleBinding] --> B[Subjects]
    A --> C[RoleRef]

    B --> D[User]
    B --> E[Group]
    B --> F[ServiceAccount]

    C --> G[ClusterRole]

    D --> H[admin@example.com]
    E --> I[cluster-admins]
    F --> J[system:serviceaccount:kube-system:cluster-admin]

    G --> K[cluster-admin]
    G --> L[system:node]
    G --> M[system:monitoring]

    style A fill:#e1f5fe
    style B fill:#f3e5f5
    style C fill:#f3e5f5
    style D fill:#e8f5e8
    style E fill:#e8f5e8
    style F fill:#e8f5e8
    style G fill:#fff3e0
```

---

## ClusterRoleBinding 예시

### 기본 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
  - kind: User
    name: admin@example.com
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

### 다중 주체 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-managers
subjects:
  # 사용자
  - kind: User
    name: cluster-admin@example.com
    apiGroup: rbac.authorization.k8s.io
  # 그룹
  - kind: Group
    name: cluster-admins
    apiGroup: rbac.authorization.k8s.io
  # 서비스 계정
  - kind: ServiceAccount
    name: cluster-admin
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

### 시스템 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:node
subjects:
  - kind: User
    name: system:node:node-1
    apiGroup: rbac.authorization.k8s.io
  - kind: User
    name: system:node:node-2
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: system:node
  apiGroup: rbac.authorization.k8s.io
```

---

## ClusterRoleBinding 타입

### 1. 관리자 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
  - kind: User
    name: admin@example.com
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

### 2. 모니터링 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: monitoring-binding
subjects:
  - kind: ServiceAccount
    name: prometheus
    namespace: monitoring
roleRef:
  kind: ClusterRole
  name: monitoring
  apiGroup: rbac.authorization.k8s.io
```

### 3. 네임스페이스 관리자 ClusterRoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: namespace-manager-binding
subjects:
  - kind: User
    name: namespace-admin@example.com
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: namespace-manager
  apiGroup: rbac.authorization.k8s.io
```

---

## ClusterRoleBinding 관리 명령어

### 기본 명령어

```bash
# ClusterRoleBinding 생성
kubectl apply -f clusterrolebinding.yaml

# ClusterRoleBinding 목록 확인
kubectl get clusterrolebindings

# ClusterRoleBinding 상세 정보 확인
kubectl describe clusterrolebinding <clusterrolebinding-name>

# ClusterRoleBinding 삭제
kubectl delete clusterrolebinding <clusterrolebinding-name>

# ClusterRoleBinding YAML 확인
kubectl get clusterrolebinding <clusterrolebinding-name> -o yaml
```

### 권한 확인 명령어

```bash
# 사용자 권한 확인
kubectl auth can-i get pods --as=user@example.com --all-namespaces

# 특정 동작 권한 확인
kubectl auth can-i create namespaces --as=user@example.com

# 모든 권한 확인
kubectl auth can-i --list --as=user@example.com --all-namespaces

# 서비스 계정 권한 확인
kubectl auth can-i get pods --as=system:serviceaccount:monitoring:prometheus --all-namespaces
```

---

## ClusterRoleBinding 사용 사례

### 1. 클러스터 관리자 설정

```yaml
# 클러스터 관리자 ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
  - apiGroups: ["*"]
    resources: ["*"]
    verbs: ["*"]
---
# 클러스터 관리자 ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-binding
subjects:
  - kind: User
    name: admin@example.com
    apiGroup: rbac.authorization.k8s.io
  - kind: Group
    name: cluster-admins
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

### 2. 시스템 모니터링 설정

```yaml
# 모니터링 ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: monitoring
rules:
  - apiGroups: [""]
    resources: ["pods", "services", "nodes", "events"]
    verbs: ["get", "list", "watch"]
  - apiGroups: ["apps"]
    resources: ["deployments", "replicasets", "statefulsets"]
    verbs: ["get", "list", "watch"]
  - apiGroups: [""]
    resources: ["pods/log"]
    verbs: ["get", "list"]
---
# Prometheus 서비스 계정
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
  namespace: monitoring
---
# 모니터링 ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: monitoring-binding
subjects:
  - kind: ServiceAccount
    name: prometheus
    namespace: monitoring
roleRef:
  kind: ClusterRole
  name: monitoring
  apiGroup: rbac.authorization.k8s.io
```

### 3. 네임스페이스 관리자 설정

```yaml
# 네임스페이스 관리자 ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: namespace-manager
rules:
  - apiGroups: [""]
    resources: ["namespaces"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
  - apiGroups: [""]
    resources: ["pods", "services", "configmaps", "secrets"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
  - apiGroups: ["apps"]
    resources: ["deployments", "replicasets", "statefulsets"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
---
# 네임스페이스 관리자 ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: namespace-manager-binding
subjects:
  - kind: User
    name: namespace-admin@example.com
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: namespace-manager
  apiGroup: rbac.authorization.k8s.io
```

---

## ClusterRoleBinding vs RoleBinding

| 특징             | ClusterRoleBinding   | RoleBinding        |
| ---------------- | -------------------- | ------------------ |
| **범위**         | 클러스터 전체        | 네임스페이스       |
| **Role 타입**    | ClusterRole만        | Role, ClusterRole  |
| **사용 사례**    | 클러스터 관리자 권한 | 일반적인 권한 관리 |
| **네임스페이스** | 모든 네임스페이스    | 특정 네임스페이스  |

---

## ClusterRoleBinding 모범 사례

### 1. 명확한 네이밍

```yaml
# 역할과 주체를 명확히 하는 이름 사용
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-team-binding # 명확한 역할
subjects:
  - kind: Group
    name: cluster-admins
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

### 2. 최소 권한 원칙

```yaml
# 필요한 최소한의 권한만 부여
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-reader-binding
subjects:
  - kind: User
    name: auditor@example.com
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-reader # 읽기 전용 권한만
  apiGroup: rbac.authorization.k8s.io
```

### 3. 시스템 컴포넌트 분리

```yaml
# 노드 관리자 ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:node-binding
subjects:
  - kind: User
    name: system:node:node-1
    apiGroup: rbac.authorization.k8s.io
  - kind: User
    name: system:node:node-2
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: system:node
  apiGroup: rbac.authorization.k8s.io
---
# 모니터링 ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: monitoring-binding
subjects:
  - kind: ServiceAccount
    name: prometheus
    namespace: monitoring
roleRef:
  kind: ClusterRole
  name: monitoring
  apiGroup: rbac.authorization.k8s.io
```

---

## 주의사항

1. **최소 권한**: 필요한 최소한의 권한만 부여해야 합니다
2. **정기 검토**: ClusterRoleBinding을 정기적으로 검토하고 정리해야 합니다
3. **테스트**: 권한 설정 후 실제 테스트를 통해 검증해야 합니다
4. **문서화**: ClusterRoleBinding의 목적과 권한을 명확히 문서화해야 합니다
5. **보안**: 클러스터 전체 권한이므로 보안에 특히 주의해야 합니다
