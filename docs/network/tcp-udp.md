---
layout: post
title: "TCP vs UDP"
parent: Network
nav_order: 2
date: 2025-07-31
excerpt: TCP와 UDP 프로토콜의 작동 방식과 차이점에 대한 설명
---

# TCP vs UDP

## 개요

TCP(Transmission Control Protocol)와 UDP(User Datagram Protocol)는 인터넷에서 데이터를 전송하는 두 가지 주요 전송 계층 프로토콜입니다. 각각의 특성과 사용 사례가 다르며, 애플리케이션의 요구사항에 따라 적절한 프로토콜을 선택해야 합니다.

## 프로토콜 비교

```mermaid
graph TB
    subgraph "TCP (Transmission Control Protocol)"
        A[연결 지향적]
        B[신뢰성 보장]
        C[순서 보장]
        D[흐름 제어]
        E[혼잡 제어]
        F[오버헤드 높음]
    end

    subgraph "UDP (User Datagram Protocol)"
        G[비연결형]
        H[신뢰성 없음]
        I[순서 보장 없음]
        J[흐름 제어 없음]
        K[혼잡 제어 없음]
        L[오버헤드 낮음]
    end
```

## TCP (Transmission Control Protocol)

### TCP의 특징

```mermaid
graph LR
    A[연결 설정] --> B[데이터 전송]
    B --> C[연결 종료]

    subgraph "신뢰성 보장"
        D[재전송]
        E[순서 보장]
        F[흐름 제어]
    end
```

#### 1. 연결 지향적 (Connection-Oriented)

- 통신 전에 연결을 설정 (3-Way Handshake)
- 통신 후 연결을 종료 (4-Way Handshake)
- 양방향 통신 지원

#### 2. 신뢰성 보장

- 데이터 손실 감지 및 재전송
- 체크섬을 통한 데이터 무결성 검증
- ACK(Acknowledgment)를 통한 수신 확인

#### 3. 순서 보장

- 시퀀스 번호를 통한 패킷 순서 관리
- 순서가 바뀐 패킷을 올바른 순서로 재조립

#### 4. 흐름 제어 (Flow Control)

- 수신자의 처리 능력을 고려한 데이터 전송 속도 조절
- 슬라이딩 윈도우 기법 사용

#### 5. 혼잡 제어 (Congestion Control)

- 네트워크 상황에 따른 전송 속도 조절
- 네트워크 혼잡을 방지

### TCP 헤더 구조

```mermaid
graph LR
    subgraph "TCP 헤더 (20-60 bytes)"
        A[Source Port<br/>16 bits]
        B[Destination Port<br/>16 bits]
        C[Sequence Number<br/>32 bits]
        D[Acknowledgment Number<br/>32 bits]
        E[Data Offset<br/>4 bits]
        F[Reserved<br/>6 bits]
        G[Flags<br/>6 bits]
        H[Window Size<br/>16 bits]
        I[Checksum<br/>16 bits]
        J[Urgent Pointer<br/>16 bits]
        K[Options<br/>0-40 bytes]
    end
```

### TCP 상태 머신

```mermaid
stateDiagram-v2
    [*] --> CLOSED
    CLOSED --> LISTEN : 서버 시작
    CLOSED --> SYN_SENT : 클라이언트 연결 요청
    SYN_SENT --> ESTABLISHED : SYN+ACK 수신 후 ACK 전송
    LISTEN --> SYN_RECEIVED : SYN 수신
    SYN_RECEIVED --> ESTABLISHED : ACK 수신
    ESTABLISHED --> FIN_WAIT_1 : FIN 전송
    ESTABLISHED --> CLOSE_WAIT : FIN 수신
    FIN_WAIT_1 --> FIN_WAIT_2 : ACK 수신
    FIN_WAIT_2 --> TIME_WAIT : FIN 수신
    CLOSE_WAIT --> LAST_ACK : FIN 전송
    LAST_ACK --> CLOSED : ACK 수신
    TIME_WAIT --> CLOSED : 2MSL 대기 후
```

### TCP 사용 사례

```mermaid
graph TB
    subgraph "TCP 사용 사례"
        A[웹 브라우징<br/>HTTP/HTTPS]
        B[이메일<br/>SMTP/POP3/IMAP]
        C[파일 전송<br/>FTP/SFTP]
        D[원격 접속<br/>SSH/Telnet]
        E[데이터베이스<br/>MySQL/PostgreSQL]
    end
```

## UDP (User Datagram Protocol)

### UDP의 특징

```mermaid
graph LR
    A[데이터 전송] --> B[완료]

    subgraph "비연결형"
        C[연결 설정 없음]
        D[연결 종료 없음]
        E[단방향 통신]
    end
```

#### 1. 비연결형 (Connectionless)

- 통신 전 연결 설정 과정 없음
- 각 패킷이 독립적으로 전송
- 빠른 전송 속도

#### 2. 신뢰성 없음

- 데이터 손실에 대한 재전송 없음
- 수신 확인(ACK) 없음
- 애플리케이션에서 신뢰성 처리

#### 3. 순서 보장 없음

- 패킷 순서가 바뀔 수 있음
- 애플리케이션에서 순서 처리

#### 4. 흐름 제어 없음

- 수신자 처리 능력 고려하지 않음
- 전송 속도 제한 없음

#### 5. 혼잡 제어 없음

- 네트워크 상황 고려하지 않음
- 네트워크 혼잡 가능성

### UDP 헤더 구조

```mermaid
graph LR
    subgraph "UDP 헤더 (8 bytes)"
        A[Source Port<br/>16 bits]
        B[Destination Port<br/>16 bits]
        C[Length<br/>16 bits]
        D[Checksum<br/>16 bits]
    end
```

### UDP 사용 사례

```mermaid
graph TB
    subgraph "UDP 사용 사례"
        A[실시간 스트리밍<br/>영상/음성]
        B[온라인 게임<br/>실시간 데이터]
        C[DNS 조회<br/>도메인 이름 해석]
        D[DHCP<br/>IP 주소 할당]
        E[SNMP<br/>네트워크 관리]
    end
```

## 상세 비교

### 1. 연결 방식

```mermaid
sequenceDiagram
    participant Client as 클라이언트
    participant Server as 서버

    Note over Client,Server: TCP 연결 과정
    Client->>Server: SYN
    Server->>Client: SYN+ACK
    Client->>Server: ACK
    Note over Client,Server: 데이터 전송
    Client->>Server: 데이터
    Server->>Client: ACK
    Note over Client,Server: 연결 종료
    Client->>Server: FIN
    Server->>Client: ACK
    Server->>Client: FIN
    Client->>Server: ACK

    Note over Client,Server: UDP 통신
    Client->>Server: 데이터 (연결 없음)
    Server->>Client: 응답 (연결 없음)
```

### 2. 데이터 전송 방식

```mermaid
graph TB
    subgraph "TCP 데이터 전송"
        A[연결 설정] --> B[데이터 전송]
        B --> C[수신 확인]
        C --> D[재전송 필요시]
        D --> E[연결 종료]
    end

    subgraph "UDP 데이터 전송"
        F[데이터 전송] --> G[완료]
    end
```

### 3. 오버헤드 비교

```mermaid
graph LR
    subgraph "TCP 오버헤드"
        A[연결 설정: 3 패킷]
        B[데이터 전송: N 패킷]
        C[수신 확인: N 패킷]
        D[연결 종료: 4 패킷]
    end

    subgraph "UDP 오버헤드"
        E[데이터 전송: N 패킷]
    end
```

## 실제 사용 예시

### 1. 웹 브라우징 (TCP)

```mermaid
sequenceDiagram
    participant Browser as 웹 브라우저
    participant WebServer as 웹 서버

    Note over Browser,WebServer: TCP 연결 설정
    Browser->>WebServer: SYN
    WebServer->>Browser: SYN+ACK
    Browser->>WebServer: ACK

    Note over Browser,WebServer: HTTP 요청/응답
    Browser->>WebServer: HTTP GET /index.html
    WebServer->>Browser: HTTP 200 OK + HTML 데이터

    Note over Browser,WebServer: 연결 종료
    Browser->>WebServer: FIN
    WebServer->>Browser: ACK
    WebServer->>Browser: FIN
    Browser->>WebServer: ACK
```

### 2. 실시간 게임 (UDP)

```mermaid
sequenceDiagram
    participant GameClient as 게임 클라이언트
    participant GameServer as 게임 서버

    Note over GameClient,GameServer: UDP 통신 (연결 없음)
    GameClient->>GameServer: 플레이어 위치 데이터
    GameServer->>GameClient: 다른 플레이어 정보
    GameClient->>GameServer: 액션 데이터
    GameServer->>GameClient: 게임 상태 업데이트
```

### 3. DNS 조회 (UDP)

```mermaid
sequenceDiagram
    participant Client as 클라이언트
    participant DNSServer as DNS 서버

    Note over Client,DNSServer: DNS 조회 (UDP)
    Client->>DNSServer: DNS Query (www.example.com)
    DNSServer->>Client: DNS Response (192.168.1.1)
```

## 성능 비교

### 1. 속도

```mermaid
graph LR
    A[UDP] --> B[빠름<br/>연결 설정 없음<br/>오버헤드 적음]
    C[TCP] --> D[상대적으로 느림<br/>연결 설정 필요<br/>오버헤드 많음]
```

### 2. 신뢰성

```mermaid
graph LR
    A[TCP] --> B[높음<br/>재전송 보장<br/>순서 보장]
    C[UDP] --> D[낮음<br/>재전송 없음<br/>순서 보장 없음]
```

### 3. 리소스 사용량

```mermaid
graph LR
    A[UDP] --> B[적음<br/>연결 상태 관리 없음<br/>버퍼 적음]
    C[TCP] --> D[많음<br/>연결 상태 관리<br/>버퍼 많음]
```

## 프로토콜 선택 가이드

### TCP를 선택해야 하는 경우

```mermaid
graph TB
    subgraph "TCP 선택 기준"
        A[데이터 무결성이 중요한 경우]
        B[순서가 중요한 경우]
        C[신뢰성이 중요한 경우]
        D[대용량 데이터 전송]
        E[파일 전송]
    end
```

### UDP를 선택해야 하는 경우

```mermaid
graph TB
    subgraph "UDP 선택 기준"
        A[실시간성이 중요한 경우]
        B[속도가 중요한 경우]
        C[간단한 요청/응답]
        D[스트리밍 서비스]
        E[게임 데이터]
    end
```

## 결론

TCP와 UDP는 각각의 장단점이 있으며, 애플리케이션의 요구사항에 따라 적절한 프로토콜을 선택해야 합니다. TCP는 신뢰성이 중요한 웹 서비스, 파일 전송 등에 적합하고, UDP는 실시간성이 중요한 스트리밍, 게임 등에 적합합니다. 현대의 네트워크 애플리케이션에서는 두 프로토콜을 상황에 맞게 혼용하여 사용하는 경우가 많습니다.
