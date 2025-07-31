---
title: "ConfigMap"
parent: Kubernetes Components
nav_order: 6
date: 2025-07-31
excerpt: ConfigMap component in Kubernetes
---

## ConfigMap 개요

ConfigMap은 Kubernetes에서 설정 데이터를 Pod와 분리하여 관리하는 리소스입니다. 애플리케이션의 설정 파일, 환경변수, 명령행 인수 등을 외부화하여 컨테이너 이미지와 독립적으로 관리할 수 있습니다.

### ConfigMap의 특징

1. **설정 분리**: 애플리케이션 코드와 설정을 분리
2. **환경별 관리**: 개발, 스테이징, 프로덕션 환경별 설정 관리
3. **동적 업데이트**: Pod 재시작 없이 설정 변경 가능 (일부 경우)
4. **보안**: 민감한 정보는 Secret을 사용하여 관리

---

## ConfigMap 구조

```mermaid
graph TD
    A[ConfigMap] --> B[Key-Value Pairs]
    A --> C[Configuration Files]
    A --> D[Environment Variables]

    B --> E[app.properties]
    B --> F[database.conf]
    B --> G[nginx.conf]

    H[Pod] --> I[Container]
    I --> J[Volume Mount]
    I --> K[Environment Variable]

    J --> L[/etc/config/app.properties]
    K --> M[APP_CONFIG=database.conf]

    style A fill:#e1f5fe
    style B fill:#f3e5f5
    style C fill:#f3e5f5
    style D fill:#f3e5f5
    style H fill:#e8f5e8
    style I fill:#fff3e0
    style J fill:#e3f2fd
    style K fill:#e3f2fd
```

---

## ConfigMap 생성 방법

### 1. 명령행에서 생성

```bash
# 키-값 쌍으로 생성
kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2

# 파일에서 생성
kubectl create configmap my-config --from-file=app.properties --from-file=database.conf

# 디렉토리에서 생성
kubectl create configmap my-config --from-file=./config/

# 환경변수 파일에서 생성
kubectl create configmap my-config --from-env-file=app.env
```

### 2. YAML 파일로 생성

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
  namespace: default
data:
  # 키-값 쌍
  log-level: "INFO"
  database-url: "postgresql://user:pass@db:5432/mydb"

  # 설정 파일
  app.properties: |
    server.port=8080
    logging.level.root=INFO
    spring.profiles.active=production

  nginx.conf: |
    server {
        listen 80;
        server_name localhost;
        
        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }
    }

  database.conf: |
    [database]
    host=db.example.com
    port=5432
    name=myapp
    user=appuser
```

---

## ConfigMap 사용 방법

### 1. 환경변수로 사용

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
    - name: app
      image: myapp:1.0
      env:
        # 단일 값
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: log-level
      # 전체 ConfigMap을 환경변수로
      envFrom:
        - configMapRef:
            name: my-config
```

### 2. 볼륨으로 마운트

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
    - name: app
      image: myapp:1.0
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
          readOnly: true
        - name: nginx-config
          mountPath: /etc/nginx/conf.d
          readOnly: true
  volumes:
    - name: config-volume
      configMap:
        name: my-config
        items:
          - key: app.properties
            path: app.properties
          - key: database.conf
            path: database.conf
    - name: nginx-config
      configMap:
        name: my-config
        items:
          - key: nginx.conf
            path: default.conf
```

### 3. 하위 경로로 마운트

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
    - name: app
      image: myapp:1.0
      volumeMounts:
        - name: config-volume
          mountPath: /etc/config
          readOnly: true
  volumes:
    - name: config-volume
      configMap:
        name: my-config
        # 전체 ConfigMap을 개별 파일로 마운트
```

---

## ConfigMap 예시

### 1. 웹 애플리케이션 설정

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: web-app-config
data:
  # 환경변수
  NODE_ENV: "production"
  PORT: "8080"
  DATABASE_URL: "postgresql://user:pass@db:5432/webapp"

  # 설정 파일
  config.json: |
    {
      "server": {
        "port": 8080,
        "host": "0.0.0.0"
      },
      "database": {
        "host": "db.example.com",
        "port": 5432,
        "name": "webapp"
      },
      "logging": {
        "level": "info",
        "format": "json"
      }
    }

  nginx.conf: |
    server {
        listen 80;
        server_name localhost;
        
        location / {
            proxy_pass http://localhost:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
```

### 2. 데이터베이스 설정

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: database-config
data:
  # PostgreSQL 설정
  postgresql.conf: |
    # 기본 설정
    listen_addresses = '*'
    port = 5432
    max_connections = 100

    # 메모리 설정
    shared_buffers = 256MB
    effective_cache_size = 1GB

    # 로깅 설정
    log_destination = 'stderr'
    logging_collector = on
    log_directory = 'log'
    log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'

  # 초기화 스크립트
  init.sql: |
    CREATE DATABASE myapp;
    CREATE USER appuser WITH PASSWORD 'password';
    GRANT ALL PRIVILEGES ON DATABASE myapp TO appuser;

  # 환경변수
  POSTGRES_DB: "myapp"
  POSTGRES_USER: "appuser"
  POSTGRES_PASSWORD: "password"
```

### 3. 마이크로서비스 설정

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: microservice-config
data:
  # API 서비스 설정
  api-service.properties: |
    server.port=8080
    spring.application.name=api-service
    spring.profiles.active=production

    # 데이터베이스 설정
    spring.datasource.url=jdbc:postgresql://db:5432/myapp
    spring.datasource.username=appuser
    spring.datasource.password=password

    # 로깅 설정
    logging.level.root=INFO
    logging.level.com.myapp=DEBUG

    # 보안 설정
    security.oauth2.client.client-id=myapp
    security.oauth2.client.client-secret=secret

  # 사용자 서비스 설정
  user-service.properties: |
    server.port=8081
    spring.application.name=user-service
    spring.profiles.active=production

    # 데이터베이스 설정
    spring.datasource.url=jdbc:postgresql://db:5432/users
    spring.datasource.username=userapp
    spring.datasource.password=password

    # 캐시 설정
    spring.cache.type=redis
    spring.redis.host=redis
    spring.redis.port=6379

  # 주문 서비스 설정
  order-service.properties: |
    server.port=8082
    spring.application.name=order-service
    spring.profiles.active=production

    # 데이터베이스 설정
    spring.datasource.url=jdbc:postgresql://db:5432/orders
    spring.datasource.username=orderapp
    spring.datasource.password=password

    # 메시징 설정
    spring.kafka.bootstrap-servers=kafka:9092
    spring.kafka.consumer.group-id=order-service
```

---

## ConfigMap 관리 명령어

### 기본 명령어

```bash
# ConfigMap 생성
kubectl create configmap my-config --from-literal=key=value
kubectl apply -f configmap.yaml

# ConfigMap 목록 확인
kubectl get configmaps
kubectl get cm

# ConfigMap 상세 정보 확인
kubectl describe configmap <configmap-name>

# ConfigMap 내용 확인
kubectl get configmap <configmap-name> -o yaml

# ConfigMap 삭제
kubectl delete configmap <configmap-name>
```

### 편집 명령어

```bash
# ConfigMap 편집
kubectl edit configmap <configmap-name>

# ConfigMap 복사
kubectl get configmap <source-configmap> -o yaml | sed 's/name: <source-configmap>/name: <target-configmap>/' | kubectl apply -f -

# ConfigMap 내보내기
kubectl get configmap <configmap-name> -o yaml > configmap-backup.yaml
```

---

## ConfigMap 업데이트

### 1. ConfigMap 업데이트

```bash
# ConfigMap 업데이트
kubectl patch configmap my-config -p '{"data":{"log-level":"DEBUG"}}'

# 또는 편집
kubectl edit configmap my-config
```

### 2. Pod 재시작 없이 업데이트

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  template:
    spec:
      containers:
        - name: app
          image: myapp:1.0
          volumeMounts:
            - name: config-volume
              mountPath: /etc/config
              readOnly: true
      volumes:
        - name: config-volume
          configMap:
            name: my-config
      # ConfigMap 변경 시 Pod 재시작
      annotations:
        checksum/config: "${CONFIG_CHECKSUM}"
```

### 3. 자동 재시작 스크립트

```bash
#!/bin/bash
# ConfigMap 변경 시 Deployment 재시작

CONFIGMAP_NAME="my-config"
DEPLOYMENT_NAME="my-app"

# ConfigMap의 체크섬 계산
CHECKSUM=$(kubectl get configmap $CONFIGMAP_NAME -o yaml | sha256sum | cut -d' ' -f1)

# Deployment 패치
kubectl patch deployment $DEPLOYMENT_NAME -p "{\"spec\":{\"template\":{\"metadata\":{\"annotations\":{\"checksum/config\":\"$CHECKSUM\"}}}}}"
```

---

## ConfigMap 사용 사례

### 1. 환경별 설정 관리

```yaml
# 개발 환경
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config-dev
  namespace: development
data:
  LOG_LEVEL: "DEBUG"
  DATABASE_URL: "postgresql://dev:dev@localhost:5432/devdb"
  API_URL: "http://localhost:3000"

---
# 스테이징 환경
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config-staging
  namespace: staging
data:
  LOG_LEVEL: "INFO"
  DATABASE_URL: "postgresql://staging:staging@db-staging:5432/stagingdb"
  API_URL: "https://api-staging.example.com"

---
# 프로덕션 환경
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config-prod
  namespace: production
data:
  LOG_LEVEL: "WARN"
  DATABASE_URL: "postgresql://prod:prod@db-prod:5432/proddb"
  API_URL: "https://api.example.com"
```

### 2. 설정 파일 관리

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: application-config
data:
  # 애플리케이션 설정
  application.yml: |
    spring:
      application:
        name: myapp
      profiles:
        active: production
      datasource:
        url: jdbc:postgresql://db:5432/myapp
        username: appuser
        password: password
      jpa:
        hibernate:
          ddl-auto: validate
        show-sql: false
      logging:
        level:
          root: INFO
          com.myapp: DEBUG

  # 로그백 설정
  logback.xml: |
    <configuration>
      <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
          <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
      </appender>
      
      <root level="INFO">
        <appender-ref ref="STDOUT" />
      </root>
    </configuration>

  # Nginx 설정
  nginx.conf: |
    events {
        worker_connections 1024;
    }

    http {
        upstream backend {
            server app:8080;
        }
        
        server {
            listen 80;
            location / {
                proxy_pass http://backend;
            }
        }
    }
```

### 3. 기능 플래그 관리

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: feature-flags
data:
  features.json: |
    {
      "newUI": true,
      "darkMode": false,
      "analytics": true,
      "betaFeatures": {
        "advancedSearch": true,
        "realTimeUpdates": false,
        "aiRecommendations": true
      },
      "experiments": {
        "abTest1": {
          "enabled": true,
          "weight": 0.5
        },
        "abTest2": {
          "enabled": false,
          "weight": 0.0
        }
      }
    }

  feature-flags.properties: |
    feature.new-ui.enabled=true
    feature.dark-mode.enabled=false
    feature.analytics.enabled=true
    feature.advanced-search.enabled=true
    feature.real-time-updates.enabled=false
    feature.ai-recommendations.enabled=true
    experiment.ab-test-1.enabled=true
    experiment.ab-test-1.weight=0.5
    experiment.ab-test-2.enabled=false
    experiment.ab-test-2.weight=0.0
```

---

## ConfigMap vs Secret

| 특징            | ConfigMap         | Secret         |
| --------------- | ----------------- | -------------- |
| **용도**        | 일반 설정 데이터  | 민감한 데이터  |
| **암호화**      | Base64 인코딩만   | 암호화 저장    |
| **볼륨 마운트** | 지원              | 지원           |
| **환경변수**    | 지원              | 지원           |
| **사용 사례**   | 애플리케이션 설정 | 비밀번호, 토큰 |

---

## 주의사항

1. **크기 제한**: ConfigMap은 1MB로 제한됩니다
2. **민감한 정보**: 비밀번호, 토큰 등은 Secret을 사용해야 합니다
3. **업데이트**: ConfigMap 변경 시 Pod 재시작이 필요할 수 있습니다
4. **네임스페이스**: ConfigMap은 네임스페이스 범위입니다
5. **버전 관리**: 설정 변경 시 버전 관리를 고려해야 합니다
