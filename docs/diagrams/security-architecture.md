# Security Architecture Diagrams

## Security Layers

```mermaid
graph TB
    subgraph "Application Security"
        A1[Authentication]
        A2[Authorization]
        A3[Input Validation]
        A4[Output Encoding]
    end

    subgraph "Network Security"
        N1[HTTPS/TLS]
        N2[Firewall Rules]
        N3[DDoS Protection]
        N4[IP Whitelisting]
    end

    subgraph "Data Security"
        D1[Encryption at Rest]
        D2[Encryption in Transit]
        D3[Secure Key Management]
        D4[Data Masking]
    end

    subgraph "Operational Security"
        O1[Audit Logging]
        O2[Monitoring]
        O3[Incident Response]
        O4[Security Testing]
    end

    style A1 fill:#e1f5ff
    style A2 fill:#e1f5ff
    style A3 fill:#e1f5ff
    style A4 fill:#e1f5ff
    style N1 fill:#ffe1e1
    style N2 fill:#ffe1e1
    style N3 fill:#ffe1e1
    style N4 fill:#ffe1e1
    style D1 fill:#e1ffe1
    style D2 fill:#e1ffe1
    style D3 fill:#e1ffe1
    style D4 fill:#e1ffe1
    style O1 fill:#fff4e1
    style O2 fill:#fff4e1
    style O3 fill:#fff4e1
    style O4 fill:#fff4e1
```

## Authentication Flow

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant Auth Service
    participant Database
    participant Redis

    User->>Frontend: Enter Credentials
    Frontend->>API: POST /api/auth/login
    API->>Auth Service: Validate Credentials
    Auth Service->>Database: Query User
    Database-->>Auth Service: User Hash
    Auth Service->>Auth Service: Verify Hash
    Auth Service-->>API: User Valid
    API->>API: Generate JWT
    API->>API: Generate Refresh Token
    API->>Redis: Store Refresh Token
    API->>Database: Update Last Login
    API-->>Frontend: Access + Refresh Token
    Frontend->>Frontend: Store Tokens
    Frontend-->>User: Login Success

    Note over Frontend,Redis: Subsequent Requests
    Frontend->>API: Request + JWT
    API->>API: Validate JWT
    API-->>Frontend: Response
```

## JWT Token Structure

```mermaid
graph LR
    subgraph "JWT Header"
        H1[alg: RS256]
        H2[typ: JWT]
    end

    subgraph "JWT Payload"
        P1[sub: user_id]
        P2[iss: claimforge]
        P3[aud: api]
        P4[exp: timestamp]
        P5[iat: timestamp]
        P6[roles: array]
        P7[permissions: array]
    end

    subgraph "JWT Signature"
        S1[RS256 Signed]
        S2[Private Key]
    end

    H1 --> P1
    P1 --> S1

    style H1 fill:#e1f5ff
    style H2 fill:#e1f5ff
    style P1 fill:#ffe1e1
    style P2 fill:#ffe1e1
    style P3 fill:#ffe1e1
    style P4 fill:#ffe1e1
    style P5 fill:#ffe1e1
    style P6 fill:#ffe1e1
    style P7 fill:#ffe1e1
    style S1 fill:#e1ffe1
    style S2 fill:#e1ffe1
```

## Role-Based Access Control (RBAC)

```mermaid
graph TB
    subgraph "Users"
        U1[User A]
        U2[User B]
        U3[User C]
    end

    subgraph "Roles"
        R1[Claimant]
        R2[Adjuster]
        R3[Reviewer]
        R4[Manager]
        R5[Admin]
    end

    subgraph "Permissions"
        P1[create:claim]
        P2[read:claim]
        P3[update:claim]
        P4[delete:claim]
        P5[approve:settlement]
        P6[manage:users]
        P7[view:analytics]
    end

    subgraph "Resources"
        Res1[Claims]
        Res2[Documents]
        Res3[Users]
        Res4[Reports]
    end

    U1 --> R1
    U2 --> R2
    U3 --> R3
    R4 --> R5

    R1 --> P1
    R1 --> P2
    R2 --> P2
    R2 --> P3
    R3 --> P2
    R3 --> P5
    R4 --> P2
    R4 --> P5
    R4 --> P7
    R5 --> P6

    P1 --> Res1
    P2 --> Res1
    P3 --> Res1
    P4 --> Res1
    P5 --> Res1
    P6 --> Res3
    P7 --> Res4

    style U1 fill:#e1f5ff
    style U2 fill:#e1f5ff
    style U3 fill:#e1f5ff
    style R1 fill:#ffe1e1
    style R2 fill:#ffe1e1
    style R3 fill:#ffe1e1
    style R4 fill:#ffe1e1
    style R5 fill:#ffe1e1
    style P1 fill:#e1ffe1
    style P2 fill:#e1ffe1
    style P3 fill:#e1ffe1
    style P4 fill:#e1ffe1
    style P5 fill:#e1ffe1
    style P6 fill:#e1ffe1
    style P7 fill:#e1ffe1
```

## Request Security Pipeline

```mermaid
graph TB
    A[Incoming Request] --> B[Rate Limit Check]
    B --> C{Within Limit?}
    C -->|No| D[429 Response]
    C -->|Yes| E[TLS Termination]
    E --> F[JWT Validation]
    F --> G{Valid Token?}
    G -->|No| H[401 Response]
    G -->|Yes| I[Role Check]
    I --> J{Authorized?}
    J -->|No| K[403 Response]
    J -->|Yes| L[Input Validation]
    L --> M{Valid Input?}
    M -->|No| N[400 Response]
    M -->|Yes| O[Business Logic]
    O --> P[Response]
    P --> Q[Security Headers]
    Q --> R[Send Response]

    style D fill:#ffcccc
    style H fill:#ff9999
    style K fill:#ff6666
    style N fill:#ff3333
    style R fill:#00cc00
```

## Data Encryption

```mermaid
graph TB
    subgraph "Encryption at Rest"
        E1[Database Encryption]
        E2[AES-256]
        E3[Key Rotation]
    end

    subgraph "Encryption in Transit"
        T1[TLS 1.3]
        T2[HTTPS Only]
        T3[HSTS Headers]
    end

    subgraph "Key Management"
        K1[AWS KMS / Azure Key Vault]
        K2[HSM Backed]
        K3[Access Controls]
    end

    subgraph "Sensitive Data"
        S1[PII]
        S2[Financial Data]
        S3[Health Data]
    end

    S1 --> E1
    S2 --> E1
    S3 --> E1
    E1 --> E2
    E2 --> E3
    E3 --> K1
    K1 --> K2
    K2 --> K3

    S1 --> T1
    S2 --> T1
    S3 --> T1
    T1 --> T2
    T2 --> T3

    style E1 fill:#e1f5ff
    style E2 fill:#e1f5ff
    style E3 fill:#e1f5ff
    style T1 fill:#ffe1e1
    style T2 fill:#ffe1e1
    style T3 fill:#ffe1e1
    style K1 fill:#e1ffe1
    style K2 fill:#e1ffe1
    style K3 fill:#e1ffe1
```

## Audit Logging

```mermaid
sequenceDiagram
    participant User
    participant API
    participant Audit Service
    participant MongoDB
    participant SIEM

    User->>API: Perform Action
    API->>API: Validate Request
    API->>Audit Service: Log Event
    Audit Service->>MongoDB: Store Log
    Audit Service->>SIEM: Forward Event
    API-->>User: Response

    Note over Audit Service,SIEM: Log includes:
    - User ID
    - Action
    - Resource
    - Timestamp
    - IP Address
    - User Agent
    - Changes (before/after)
```

## Security Monitoring

```mermaid
graph TB
    subgraph "Security Events"
        E1[Failed Logins]
        E2[Unauthorized Access]
        E3[Data Exfiltration]
        E4[Anomalous Behavior]
    end

    subgraph "Monitoring Tools"
        M1[SIEM]
        M2[IDS/IPS]
        M3[WAF]
        M4[APM]
    end

    subgraph "Alerting"
        A1[Email]
        A2[Slack]
        A3[PagerDuty]
        A4[SMS]
    end

    subgraph "Response"
        R1[Auto Block]
        R2[Manual Review]
        R3[Incident Ticket]
    end

    E1 --> M1
    E2 --> M1
    E3 --> M2
    E4 --> M3

    M1 --> A1
    M2 --> A2
    M3 --> A3
    M4 --> A4

    A1 --> R1
    A2 --> R2
    A3 --> R3

    style E1 fill:#e1f5ff
    style E2 fill:#e1f5ff
    style E3 fill:#e1f5ff
    style E4 fill:#e1f5ff
    style M1 fill:#ffe1e1
    style M2 fill:#ffe1e1
    style M3 fill:#ffe1e1
    style M4 fill:#ffe1e1
    style A1 fill:#e1ffe1
    style A2 fill:#e1ffe1
    style A3 fill:#e1ffe1
    style A4 fill:#e1ffe1
```

## OWASP Top 10 Mitigations

```mermaid
graph LR
    subgraph "OWASP Risks"
        O1[A01 Broken Access Control]
        O2[A02 Cryptographic Failures]
        O3[A03 Injection]
        O4[A04 Insecure Design]
        O5[A05 Security Misconfiguration]
        O6[A06 Vulnerable Components]
        O7[A07 Auth Failures]
        O8[A08 Data Integrity]
        O9[A09 Logging]
        O10[A10 SSRF]
    end

    subgraph "Mitigations"
        M1[RBAC Implementation]
        M2[Strong Encryption]
        M3[Parameterized Queries]
        M4[Threat Modeling]
        M5[Security Headers]
        M6[Dependency Scanning]
        M7[MFA Support]
        M8[Digital Signatures]
        M9[Audit Trails]
        M10[Network Segmentation]
    end

    O1 --> M1
    O2 --> M2
    O3 --> M3
    O4 --> M4
    O5 --> M5
    O6 --> M6
    O7 --> M7
    O8 --> M8
    O9 --> M9
    O10 --> M10

    style O1 fill:#ffcccc
    style O2 fill:#ffcccc
    style O3 fill:#ffcccc
    style O4 fill:#ffcccc
    style O5 fill:#ffcccc
    style O6 fill:#ffcccc
    style O7 fill:#ffcccc
    style O8 fill:#ffcccc
    style O9 fill:#ffcccc
    style O10 fill:#ffcccc
    style M1 fill:#ccffcc
    style M2 fill:#ccffcc
    style M3 fill:#ccffcc
    style M4 fill:#ccffcc
    style M5 fill:#ccffcc
    style M6 fill:#ccffcc
    style M7 fill:#ccffcc
    style M8 fill:#ccffcc
    style M9 fill:#ccffcc
    style M10 fill:#ccffcc
```
