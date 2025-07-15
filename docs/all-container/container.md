---
layout: post
title: "What-is-Container"
parent: Container
date: 2025-07-15
nav_order: 1
excerpt: Description of container
---

# What is container

- 컨테이너는 애플리케이션과 그 실행에 필요한 모든 라이브러리, 설정 파일, 종속성을 하나의 패키지로 묶어, 어디서든 일관되게 실행할 수 있도록 해주는 기술입니다.
- 컨테이너는 운영체제 수준에서 격리되어 실행되며, 가상머신보다 가볍고 빠릅니다.

- **주요 특징**

  - 애플리케이션 실행 환경을 표준화
    - web app
      - node app
    - server app
      - spring boot application
  - 빠른 배포 및 확장 가능
  - 리소스 효율적(가상머신보다 오버헤드 적음)
  - 격리된 환경 제공(보안성↑)

- **개발자가 알아야 할 점**
  - 컨테이너는 호스트 OS의 커널을 공유함
  - 컨테이너 내부와 외부의 네트워크, 파일시스템, 프로세스가 분리됨
  - 대표적인 컨테이너 엔진: Docker, containerd, crio

# What is container image

- 컨테이너 이미지는 컨테이너를 실행하기 위한 모든 파일, 라이브러리, 설정, 코드 등을 포함한 불변(immutable)한 패키지입니다.
- 이미지는 여러 계층(layer)으로 구성되어 있고, 읽기 전용입니다.

- **주요 특징**

  - 컨테이너 실행의 '설계도' 역할
  - Dockerfile 등으로 이미지를 정의하고 빌드
  - 이미지 레지스트리(예: Docker Hub, Google Container Registry, harbor, docker registry)에 저장 및 배포

- **개발자가 알아야 할 점**
  - 이미지는 계층 구조로 되어 있어, 중복된 계층은 캐싱되어 효율적
  - 이미지를 작게 만들기 위해 불필요한 파일/패키지 제외 필요
  - 보안 취약점이 없는 베이스 이미지를 사용하는 것이 중요

# How to write a Dockerfile

Dockerfile은 컨테이너 이미지를 만들기 위한 설정 파일로, 이미지 빌드 과정을 단계별로 명시합니다.

## 기본 문법

- `FROM`: 베이스 이미지 지정 (필수)
- `RUN`: 명령어 실행 (이미지 빌드 시)
- `COPY`/`ADD`: 파일/디렉토리 복사
- `WORKDIR`: 작업 디렉토리 설정
- `ENV`: 환경 변수 설정
- `EXPOSE`: 컨테이너가 열 포트 지정(문서화 목적)
- `CMD`/`ENTRYPOINT`: 컨테이너 시작 시 실행할 명령

## 예시: Node.js 애플리케이션

```dockerfile
# 1. 베이스 이미지 지정
FROM node:18-alpine

# 2. 작업 디렉토리 생성 및 이동
WORKDIR /app

# 3. 의존성 파일 복사 및 설치
COPY package*.json ./
RUN npm install

# 4. 소스 코드 복사
# COPY {local directory or file} {container directory or file}
COPY . .

# 5. 컨테이너가 열 포트 지정(문서화 목적)
EXPOSE 3000

# 6. 컨테이너 시작 시 실행할 명령
CMD [ "npm", "start" ]
```

## 개발자가 알아야 할 점

- 불필요한 파일은 `.dockerignore`로 제외하여 이미지 용량 최소화
- 보안상 신뢰할 수 있는 베이스 이미지를 사용
- 멀티스테이지 빌드로 빌드/런타임 환경 분리 가능
- 계층 구조를 이해하고, 캐시 효율을 높이기 위해 자주 변경되는 파일은 아래쪽에 COPY
