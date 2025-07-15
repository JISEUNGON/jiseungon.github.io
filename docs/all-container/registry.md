---
layout: post
title: "Docker Registry"
parent: all-container
date: 2025-07-15
nav_order: 1
excerpt: Description of docker registry
---

# What is Docker registry

- Docker Registry는 컨테이너 이미지를 저장하고 배포하는 중앙 저장소입니다. 개발자는 이미지를 registry에 업로드(push)하고, 필요할 때 다운로드(pull)하여 컨테이너를 실행할 수 있습니다.

  - 이미지 버전 관리 및 공유
  - CI/CD 파이프라인에서 이미지 배포 자동화에 필수
  - 사설/공용 registry 모두 존재

# Kind of docker registry

- **공식 Docker Hub**: hub.docker.com, 가장 널리 사용되는 공개 registry
- **Harbor**: 오픈소스 기반의 엔터프라이즈용 프라이빗 registry, 보안/정책/감사 기능 강화
- **Google Container Registry(GCR), Amazon ECR, GitHub Container Registry** 등 다양한 클라우드 기반 registry

## Docker registry

- Docker에서 기본적으로 사용하는 공개 registry(hub.docker.com)
- 무료로 이미지 업로드/다운로드 가능(공개/비공개 저장소 지원)
- 전 세계적으로 가장 많이 사용됨
- 인증, 자동 빌드, 웹 UI 제공

## Harbor

- CNCF에서 관리하는 오픈소스 프라이빗 registry
- 엔터프라이즈 환경에 적합(사용자/권한 관리, 이미지 서명, 취약점 스캔, 감사 로그 등 지원)
- 자체 서버에 설치하여 운영 가능
- LDAP, AD 등 외부 인증 연동 지원

---

## Registry 비교

| 항목             | Docker Hub     | Harbor                 |
| ---------------- | -------------- | ---------------------- |
| 공개/비공개      | 모두 지원      | 모두 지원              |
| 설치 방식        | 클라우드(공식) | 온프레미스(직접 설치)  |
| 보안/정책        | 기본 제공      | 고급(정책, 감사, 서명) |
| 취약점 스캔      | 일부 제공      | 기본 제공              |
| 사용자/권한 관리 | 제한적         | 세분화/강력            |
| 외부 인증        | 제한적         | LDAP/AD 등 지원        |
| 커뮤니티/지원    | 매우 활발      | 활발                   |

---

## 이미지 올리기(push)와 받기(pull)

### 이미지 push (업로드)

1. Docker Hub/Harbor에 로그인
   ```bash
   docker login [registry 주소]
   ```
2. 이미지 태그 지정(예: myrepo/myimage:tag)
   ```bash
   docker tag [이미지명] [registry 주소]/[저장소]/[이미지명]:[태그]
   ```
3. 이미지 push
   ```bash
   docker push [registry 주소]/[저장소]/[이미지명]:[태그]
   ```

### 이미지 pull (다운로드)

```bash
docker pull [registry 주소]/[저장소]/[이미지명]:[태그]
```

- 공식 Docker Hub는 registry 주소 생략 가능(예: `docker pull nginx:latest`)
- Harbor 등 사설 registry는 주소 명시 필요(예: `docker pull harbor.example.com/myrepo/myimage:tag`)
