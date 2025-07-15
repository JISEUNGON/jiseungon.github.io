---
layout: post
title: "What-is-Helm"
parent: Helm
date: 2025-07-15
nav_order: 1
excerpt: Description of helm
---

# Helm이란?

- Kubernetes 환경에서 여러 서비스를 효율적으로 배포·관리할 수 있도록 도와주는 패키지 매니저입니다.
- Helm Chart를 통해 서비스별 설정과 리소스를 템플릿화하여 관리합니다.

## Helm 사용 시 장점

- 여러 서비스를 효율적으로 배포, 관리할 수 있다.
- Git을 통해 버전 관리가 용이하다.

---

## Helm Chart 구조

```
.
├── Chart.yaml     # chart 버전 및 정보
├── templates/     # 실제 yaml file 들이 들어가 있다.
└── values.yaml    # 변수들
```
