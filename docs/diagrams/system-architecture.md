# System Architecture Diagram

## High-Level Architecture

```mermaid
graph TB
    subgraph "Client Layer"
        A[Web Browser]
        B[React Frontend]
    end

    subgraph "API Gateway Layer"
        C[Load Balancer]
        D[API Gateway]
    end

    subgraph "Application Layer"
        E[Backend API 1]
        F[Backend API 2]
        G[Backend API 3]
    end

    subgraph "Data Layer"
        H[(PostgreSQL)]
        I[(MongoDB)]
        J[(Redis)]
    end

    subgraph "External Services"
        K[Authentication Service]
        L[Notification Service]
        M[File Storage]
    end

    A --> B
    B --> C
    C --> D
    D --> E
    D --> F
    D --> G
    E --> H
    E --> I
    E --> J
    F --> H
    F --> I
    F --> J
    G --> H
    G --> I
    G --> J
    E --> K
    F --> L
    G --> M

    style A fill:#e1f5ff
    style B fill:#b3d9ff
    style C fill:#ffe1e1
    style D fill:#ffcccc
    style E fill:#e1ffe1
    style F fill:#e1ffe1
    style G fill:#e1ffe1
    style H fill:#fff4e1
    style I fill:#fff4e1
    style J fill:#fff4e1
    style K fill:#f0e1ff
    style L fill:#f0e1ff
    style M fill:#f0e1ff
```

## Component Interactions

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant Auth
    participant Postgres
    participant Mongo
    participant Redis

    User->>Frontend: Login Request
    Frontend->>API: POST /api/auth/login
    API->>Auth: Validate Credentials
    Auth->>Postgres: Query User
    Postgres-->>Auth: User Data
    Auth-->>API: JWT Token
    API->>Redis: Cache Session
    API-->>Frontend: Token + User Data
    Frontend-->>User: Dashboard

    User->>Frontend: Create Claim
    Frontend->>API: POST /api/claims
    API->>Postgres: Create Claim Record
    API->>Mongo: Store Documents
    API->>Redis: Invalidate Cache
    API-->>Frontend: Claim ID
    Frontend-->>User: Success Message
```

## Data Flow Architecture

```mermaid
graph LR
    A[User Action] --> B[Frontend Component]
    B --> C[React Query Hook]
    C --> D[API Service Layer]
    D --> E[HTTP Request]
    E --> F[Backend Controller]
    F --> G[Service Layer]
    G --> H[Validation]
    H --> I[Business Logic]
    I --> J[Database Operations]
    J --> K[PostgreSQL/MongoDB]
    K --> L[Response]
    L --> M[Redis Cache Update]
    M --> N[API Response]
    N --> O[Frontend Update]
    O --> P[UI Refresh]

    style A fill:#e1f5ff
    style B fill:#b3d9ff
    style C fill:#99ccff
    style D fill:#66b3ff
    style E fill:#ffcccc
    style F fill:#ff9999
    style G fill:#ff6666
    style H fill:#ffe1e1
    style I fill:#ffcccc
    style J fill:#ff9999
    style K fill:#ff6666
    style L fill:#e1ffe1
    style M fill:#ccffcc
    style N fill:#99ff99
    style O fill:#66ff66
    style P fill:#33ff33
```

## Database Schema Overview

```mermaid
erDiagram
    USERS ||--o{ CLAIMS : creates
    USERS ||--o{ AUDIT_LOGS : generates
    USERS {
        uuid id PK
        string email UK
        string password_hash
        string role
        timestamp created_at
        timestamp updated_at
    }

    CLAIMS ||--o{ CLAIM_DOCUMENTS : has
    CLAIMS ||--o{ CLAIM_NOTES : has
    CLAIMS ||--|| CLAIM_STATUS : has
    CLAIMS {
        uuid id PK
        uuid user_id FK
        string claim_number UK
        decimal amount
        string status
        timestamp filed_date
        timestamp updated_at
    }

    CLAIM_STATUS {
        string code PK
        string label
        string description
    }

    CLAIM_DOCUMENTS {
        uuid id PK
        uuid claim_id FK
        string file_url
        string document_type
        timestamp uploaded_at
    }

    CLAIM_NOTES {
        uuid id PK
        uuid claim_id FK
        uuid author_id FK
        text content
        timestamp created_at
    }

    AUDIT_LOGS {
        uuid id PK
        uuid user_id FK
        string action
        jsonb details
        timestamp created_at
    }
```

## Security Architecture

```mermaid
graph TB
    subgraph "Security Layers"
        A[Network Security]
        B[API Security]
        C[Application Security]
        D[Data Security]
    end

    A --> A1[HTTPS/TLS]
    A --> A2[Rate Limiting]
    A --> A3[IP Whitelisting]

    B --> B1[JWT Authentication]
    B --> B2[RBAC Authorization]
    B --> B3[Request Validation]

    C --> C1[Input Sanitization]
    C --> C2[XSS Protection]
    C --> C3[CSRF Tokens]

    D --> D1[Encryption at Rest]
    D --> D2[Encryption in Transit]
    D --> D3[Audit Logging]

    style A fill:#ffe1e1
    style B fill:#ffcccc
    style C fill:#ffb3b3
    style D fill:#ff9999
```

## Deployment Architecture

```mermaid
graph TB
    subgraph "Development"
        Dev[Local Docker Compose]
    end

    subgraph "Staging"
        Staging[Staging Environment]
        StagingDB[(Staging DB)]
    end

    subgraph "Production"
        Prod[Production Cluster]
        ProdDB[(Production DB)]
        CDN[CDN]
        LB[Load Balancer]
    end

    Dev --> Staging
    Staging --> Prod
    Prod --> CDN
    Prod --> LB
    LB --> ProdDB

    style Dev fill:#e1ffe1
    style Staging fill:#ccffcc
    style Prod fill:#99ff99
    style CDN fill:#66ff66
    style LB fill:#33ff33
```
