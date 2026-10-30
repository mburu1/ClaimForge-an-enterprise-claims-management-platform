# Database Design Diagrams

## Multi-Database Architecture

```mermaid
graph TB
    subgraph "PostgreSQL - Relational Data"
        P1[Users]
        P2[Claims]
        P3[Claim Status]
        P4[Roles]
        P5[Permissions]
    end

    subgraph "MongoDB - Document Storage"
        M1[Claim Documents]
        M2[Audit Logs]
        M3[Activity Streams]
        M4[File Metadata]
    end

    subgraph "Redis - Cache & Sessions"
        R1[Session Cache]
        R2[Query Cache]
        R3[Rate Limiting]
        R4[Real-time Updates]
    end

    subgraph "Application"
        A[Backend API]
    end

    A --> P1
    A --> P2
    A --> P3
    A --> P4
    A --> P5
    A --> M1
    A --> M2
    A --> M3
    A --> M4
    A --> R1
    A --> R2
    A --> R3
    A --> R4

    style P1 fill:#e1f5ff
    style P2 fill:#e1f5ff
    style P3 fill:#e1f5ff
    style P4 fill:#e1f5ff
    style P5 fill:#e1f5ff
    style M1 fill:#ffe1e1
    style M2 fill:#ffe1e1
    style M3 fill:#ffe1e1
    style M4 fill:#ffe1e1
    style R1 fill:#e1ffe1
    style R2 fill:#e1ffe1
    style R3 fill:#e1ffe1
    style R4 fill:#e1ffe1
```

## PostgreSQL Schema

```mermaid
erDiagram
    USERS ||--o{ CLAIMS : creates
    USERS ||--o{ AUDIT_LOGS : generates
    USERS ||--o{ USER_ROLES : has
    USERS ||--o{ CLAIM_NOTES : writes
    ROLES ||--o{ USER_ROLES : assigned_to
    ROLES ||--o{ ROLE_PERMISSIONS : has
    PERMISSIONS ||--o{ ROLE_PERMISSIONS : granted_to
    CLAIMS ||--o{ CLAIM_DOCUMENTS : has
    CLAIMS ||--o{ CLAIM_NOTES : has
    CLAIMS ||--|| CLAIM_STATUS : has
    CLAIMS ||--o{ CLAIM_HISTORY : tracks
    CLAIM_STATUS ||--o{ CLAIM_STATUS_TRANSITIONS : allows

    USERS {
        uuid id PK
        string email UK
        string password_hash
        string first_name
        string last_name
        string phone
        boolean is_active
        timestamp created_at
        timestamp updated_at
        timestamp last_login
    }

    ROLES {
        uuid id PK
        string name UK
        string description
        timestamp created_at
        timestamp updated_at
    }

    PERMISSIONS {
        uuid id PK
        string name UK
        string resource
        string action
        string description
    }

    USER_ROLES {
        uuid id PK
        uuid user_id FK
        uuid role_id FK
        timestamp assigned_at
        timestamp expires_at
    }

    ROLE_PERMISSIONS {
        uuid id PK
        uuid role_id FK
        uuid permission_id FK
        timestamp granted_at
    }

    CLAIMS {
        uuid id PK
        string claim_number UK
        uuid user_id FK
        uuid assigned_to FK
        uuid status_id FK
        decimal claim_amount
        decimal settlement_amount
        string policy_number
        date incident_date
        date filed_date
        date closed_date
        text description
        jsonb metadata
        timestamp created_at
        timestamp updated_at
    }

    CLAIM_STATUS {
        uuid id PK
        string code UK
        string label
        string description
        integer sort_order
    }

    CLAIM_DOCUMENTS {
        uuid id PK
        uuid claim_id FK
        string document_type
        string file_name
        string file_url
        string file_hash
        bigint file_size
        string mime_type
        timestamp uploaded_at
        timestamp uploaded_by
    }

    CLAIM_NOTES {
        uuid id PK
        uuid claim_id FK
        uuid author_id FK
        text content
        boolean is_internal
        timestamp created_at
    }

    CLAIM_HISTORY {
        uuid id PK
        uuid claim_id FK
        uuid changed_by FK
        uuid old_status_id FK
        uuid new_status_id FK
        text change_reason
        timestamp changed_at
    }

    AUDIT_LOGS {
        uuid id PK
        uuid user_id FK
        string action
        string resource_type
        uuid resource_id
        jsonb old_values
        jsonb new_values
        string ip_address
        string user_agent
        timestamp created_at
    }

    CLAIM_STATUS_TRANSITIONS {
        uuid id PK
        uuid from_status_id FK
        uuid to_status_id FK
        boolean requires_approval
        jsonb allowed_roles
    }
```

## MongoDB Collections

```mermaid
graph TB
    subgraph "MongoDB Collections"
        D1[claim_documents]
        D2[audit_logs]
        D3[activity_streams]
        D4[file_metadata]
        D5[notifications]
        D6[search_index]
    end

    subgraph "claim_documents Schema"
        C1{_id: ObjectId}
        C2{claim_id: UUID}
        C3{document_type: String}
        C4{file_url: String}
        C5{metadata: Object}
        C6{version: Number}
        C7{created_at: Date}
    end

    subgraph "audit_logs Schema"
        A1{_id: ObjectId}
        A2{user_id: UUID}
        A3{action: String}
        A4{resource: String}
        A5{changes: Object}
        A6{timestamp: Date}
    end

    D1 --> C1
    D2 --> A1

    style D1 fill:#e1f5ff
    style D2 fill:#e1f5ff
    style D3 fill:#e1f5ff
    style D4 fill:#e1f5ff
    style D5 fill:#e1f5ff
    style D6 fill:#e1f5ff
```

## Data Flow Between Databases

```mermaid
sequenceDiagram
    participant App as Application
    participant PG as PostgreSQL
    participant Mongo as MongoDB
    participant Redis as Redis

    App->>PG: Create Claim Record
    PG-->>App: Claim ID
    App->>Mongo: Store Document Metadata
    Mongo-->>App: Document ID
    App->>Redis: Cache Claim Data
    Redis-->>App: Cached
    App->>Mongo: Log Audit Event
    Mongo-->>App: Logged

    Note over App,Redis: Read Flow
    App->>Redis: Check Cache
    Redis-->>App: Cache Hit/Miss
    alt Cache Miss
        App->>PG: Query Claim
        PG-->>App: Claim Data
        App->>Mongo: Query Documents
        Mongo-->>App: Documents
        App->>Redis: Update Cache
    end
```

## Indexing Strategy

```mermaid
graph TB
    subgraph "PostgreSQL Indexes"
        I1[Users: email]
        I2[Claims: claim_number]
        I3[Claims: user_id]
        I4[Claims: status_id]
        I5[Claims: created_at]
        I6[Audit: user_id]
        I7[Audit: created_at]
    end

    subgraph "MongoDB Indexes"
        M1[claim_documents: claim_id]
        M2[claim_documents: document_type]
        M3[audit_logs: user_id]
        M4[audit_logs: timestamp]
        M5[activity_streams: user_id]
    end

    subgraph "Redis Keys"
        R1[session:{user_id}]
        R2[claim:{claim_id}]
        R3[user_claims:{user_id}]
        R4[rate_limit:{user_id}]
    end

    style I1 fill:#e1f5ff
    style I2 fill:#e1f5ff
    style I3 fill:#e1f5ff
    style I4 fill:#e1f5ff
    style I5 fill:#e1f5ff
    style I6 fill:#e1f5ff
    style I7 fill:#e1f5ff
    style M1 fill:#ffe1e1
    style M2 fill:#ffe1e1
    style M3 fill:#ffe1e1
    style M4 fill:#ffe1e1
    style M5 fill:#ffe1e1
    style R1 fill:#e1ffe1
    style R2 fill:#e1ffe1
    style R3 fill:#e1ffe1
    style R4 fill:#e1ffe1
```

## Database Migration Flow

```mermaid
graph LR
    A[Migration Script] --> B[Version Control]
    B --> C[Apply to Dev]
    C --> D[Test Data]
    D --> E[Apply to Staging]
    E --> F[Integration Tests]
    F --> G[Apply to Production]
    G --> H[Backup Rollback]

    style A fill:#e1f5ff
    style B fill:#ffe1e1
    style C fill:#ffcccc
    style D fill:#ffb3b3
    style E fill:#ff9999
    style F fill:#ff6666
    style G fill:#ff3333
    style H fill:#e1ffe1
```

## Data Consistency Strategy

```mermaid
graph TB
    subgraph "ACID Transactions"
        T1[PostgreSQL]
        T2[Atomic Operations]
        T3[Rollback Support]
    end

    subgraph "Eventual Consistency"
        E1[MongoDB]
        E2[Async Replication]
        E3[Conflict Resolution]
    end

    subgraph "Cache Invalidation"
        C1[Redis]
        C2[TTL-based]
        C3[Write-through]
    end

    T1 --> T2
    T2 --> T3
    E1 --> E2
    E2 --> E3
    C1 --> C2
    C2 --> C3

    style T1 fill:#e1f5ff
    style T2 fill:#e1f5ff
    style T3 fill:#e1f5ff
    style E1 fill:#ffe1e1
    style E2 fill:#ffe1e1
    style E3 fill:#ffe1e1
    style C1 fill:#e1ffe1
    style C2 fill:#e1ffe1
    style C3 fill:#e1ffe1
```

## Backup Strategy

```mermaid
graph TB
    subgraph "PostgreSQL Backup"
        P1[Daily Full Backup]
        P2[Hourly WAL]
        P3[Point-in-Time Recovery]
    end

    subgraph "MongoDB Backup"
        M1[Daily Snapshot]
        M2[Oplog]
        M3[Incremental Backup]
    end

    subgraph "Redis Backup"
        R1[RDB Snapshot]
        R2[AOF Log]
        R3[Replication]
    end

    subgraph "Storage"
        S1[Local Storage]
        S2[Cloud Storage]
        S3[Offsite Backup]
    end

    P1 --> S1
    P2 --> S1
    P3 --> S2
    M1 --> S1
    M2 --> S1
    M3 --> S2
    R1 --> S1
    R2 --> S1
    R3 --> S3

    style P1 fill:#e1f5ff
    style P2 fill:#e1f5ff
    style P3 fill:#e1f5ff
    style M1 fill:#ffe1e1
    style M2 fill:#ffe1e1
    style M3 fill:#ffe1e1
    style R1 fill:#e1ffe1
    style R2 fill:#e1ffe1
    style R3 fill:#e1ffe1
```
