# API Design Diagrams

## API Architecture Overview

```mermaid
graph TB
    subgraph "REST API Endpoints"
        A[/api/auth]
        B[/api/users]
        C[/api/claims]
        D[/api/documents]
        E[/api/audit]
        F[/api/analytics]
    end

    subgraph "Authentication Middleware"
        M1[JWT Validation]
        M2[Role Check]
        M3[Rate Limiting]
    end

    subgraph "Controllers"
        C1[Auth Controller]
        C2[User Controller]
        C3[Claim Controller]
        C4[Document Controller]
        C5[Audit Controller]
        C6[Analytics Controller]
    end

    subgraph "Services"
        S1[Auth Service]
        S2[User Service]
        S3[Claim Service]
        S4[Document Service]
        S5[Audit Service]
        S6[Analytics Service]
    end

    A --> M1
    B --> M1
    C --> M1
    D --> M1
    E --> M1
    F --> M1

    M1 --> M2
    M2 --> M3

    A --> C1
    B --> C2
    C --> C3
    D --> C4
    E --> C5
    F --> C6

    C1 --> S1
    C2 --> S2
    C3 --> S3
    C4 --> S4
    C5 --> S5
    C6 --> S6

    style A fill:#e1f5ff
    style B fill:#e1f5ff
    style C fill:#e1f5ff
    style D fill:#e1f5ff
    style E fill:#e1f5ff
    style F fill:#e1f5ff
    style M1 fill:#ffe1e1
    style M2 fill:#ffcccc
    style M3 fill:#ffb3b3
```

## API Request Flow

```mermaid
sequenceDiagram
    participant Client
    participant Router
    participant Middleware
    participant Controller
    participant Service
    participant Database
    participant Response

    Client->>Router: HTTP Request
    Router->>Middleware: Pass Request
    Middleware->>Middleware: Validate JWT
    Middleware->>Middleware: Check Permissions
    Middleware->>Middleware: Rate Limit Check
    Middleware->>Controller: Validated Request
    Controller->>Controller: Validate Input
    Controller->>Service: Call Business Logic
    Service->>Database: Query/Update
    Database-->>Service: Data
    Service-->>Controller: Result
    Controller->>Response: Format Response
    Response-->>Client: HTTP Response
```

## Authentication API

```mermaid
graph LR
    subgraph "Auth Endpoints"
        A1[POST /api/auth/register]
        A2[POST /api/auth/login]
        A3[POST /api/auth/logout]
        A4[POST /api/auth/refresh]
        A5[POST /api/auth/forgot-password]
        A6[POST /api/auth/reset-password]
    end

    subgraph "Responses"
        R1[201 Created + JWT]
        R2[200 OK + JWT]
        R3[200 OK]
        R4[200 OK + New JWT]
        R5[200 OK + Email Sent]
        R6[200 OK]
    end

    A1 --> R1
    A2 --> R2
    A3 --> R3
    A4 --> R4
    A5 --> R5
    A6 --> R6

    style A1 fill:#e1f5ff
    style A2 fill:#e1f5ff
    style A3 fill:#e1f5ff
    style A4 fill:#e1f5ff
    style A5 fill:#e1f5ff
    style A6 fill:#e1f5ff
```

## Claims API

```mermaid
graph TB
    subgraph "Claims CRUD"
        C1[POST /api/claims]
        C2[GET /api/claims]
        C3[GET /api/claims/:id]
        C4[PUT /api/claims/:id]
        C5[DELETE /api/claims/:id]
    end

    subgraph "Claims Actions"
        A1[POST /api/claims/:id/assign]
        A2[POST /api/claims/:id/status]
        A3[POST /api/claims/:id/settlement]
        A4[GET /api/claims/:id/history]
    end

    subgraph "Claims Filters"
        F1[?status=]
        F2[?priority=]
        F3[?assignee=]
        F4[?date_from=]
        F5[?date_to=]
    end

    C2 --> F1
    C2 --> F2
    C2 --> F3
    C2 --> F4
    C2 --> F5

    style C1 fill:#e1f5ff
    style C2 fill:#e1f5ff
    style C3 fill:#e1f5ff
    style C4 fill:#e1f5ff
    style C5 fill:#e1f5ff
    style A1 fill:#ffe1e1
    style A2 fill:#ffe1e1
    style A3 fill:#ffe1e1
    style A4 fill:#ffe1e1
```

## Users API

```mermaid
graph LR
    subgraph "User Management"
        U1[GET /api/users]
        U2[GET /api/users/:id]
        U3[PUT /api/users/:id]
        U4[DELETE /api/users/:id]
    end

    subgraph "Role Management"
        R1[GET /api/users/:id/roles]
        R2[POST /api/users/:id/roles]
        R3[DELETE /api/users/:id/roles/:roleId]
    end

    subgraph "Permissions"
        P1[GET /api/permissions]
        P2[GET /api/roles/:roleId/permissions]
    end

    U1 --> R1
    U2 --> R2
    U3 --> R3
    P1 --> P2

    style U1 fill:#e1f5ff
    style U2 fill:#e1f5ff
    style U3 fill:#e1f5ff
    style U4 fill:#e1f5ff
    style R1 fill:#ffe1e1
    style R2 fill:#ffe1e1
    style R3 fill:#ffe1e1
    style P1 fill:#e1ffe1
    style P2 fill:#ccffcc
```

## Documents API

```mermaid
graph TB
    subgraph "Document Operations"
        D1[POST /api/documents/upload]
        D2[GET /api/documents/:id]
        D3[GET /api/documents/:id/download]
        D4[DELETE /api/documents/:id]
    end

    subgraph "Document Metadata"
        M1[GET /api/documents/:id/metadata]
        M2[PUT /api/documents/:id/metadata]
    end

    subgraph "Document Associations"
        A1[GET /api/claims/:id/documents]
        A2[POST /api/claims/:id/documents]
        A3[DELETE /api/claims/:id/documents/:docId]
    end

    D1 --> A2
    D2 --> M1
    D3 --> M1
    A1 --> D2

    style D1 fill:#e1f5ff
    style D2 fill:#e1f5ff
    style D3 fill:#e1f5ff
    style D4 fill:#e1f5ff
    style M1 fill:#ffe1e1
    style M2 fill:#ffcccc
    style A1 fill:#e1ffe1
    style A2 fill:#ccffcc
    style A3 fill:#99ff99
```

## Audit API

```mermaid
graph LR
    subgraph "Audit Logs"
        L1[GET /api/audit/logs]
        L2[GET /api/audit/logs/:id]
    end

    subgraph "Audit Filters"
        F1[?user_id=]
        F2[?action=]
        F3[?resource=]
        F4[?date_from=]
        F5[?date_to=]
    end

    subgraph "Audit Export"
        E1[GET /api/audit/export]
        E2[POST /api/audit/report]
    end

    L1 --> F1
    L1 --> F2
    L1 --> F3
    L1 --> F4
    L1 --> F5
    L1 --> E1
    L1 --> E2

    style L1 fill:#e1f5ff
    style L2 fill:#e1f5ff
    style F1 fill:#ffe1e1
    style F2 fill:#ffe1e1
    style F3 fill:#ffe1e1
    style F4 fill:#ffe1e1
    style F5 fill:#ffe1e1
    style E1 fill:#e1ffe1
    style E2 fill:#ccffcc
```

## Analytics API

```mermaid
graph TB
    subgraph "Dashboard Metrics"
        M1[GET /api/analytics/dashboard]
        M2[GET /api/analytics/claims-summary]
        M3[GET /api/analytics/settlement-stats]
    end

    subgraph "Reports"
        R1[GET /api/analytics/reports/daily]
        R2[GET /api/analytics/reports/weekly]
        R3[GET /api/analytics/reports/monthly]
    end

    subgraph "Custom Queries"
        Q1[POST /api/analytics/query]
        Q2[GET /api/analytics/saved-queries]
    end

    M1 --> R1
    M2 --> R2
    M3 --> R3
    Q1 --> Q2

    style M1 fill:#e1f5ff
    style M2 fill:#e1f5ff
    style M3 fill:#e1f5ff
    style R1 fill:#ffe1e1
    style R2 fill:#ffcccc
    style R3 fill:#ffb3b3
    style Q1 fill:#e1ffe1
    style Q2 fill:#ccffcc
```

## Error Handling Flow

```mermaid
graph TB
    A[Request] --> B{Valid?}
    B -->|No| C[400 Bad Request]
    B -->|Yes| D{Authenticated?}
    D -->|No| E[401 Unauthorized]
    D -->|Yes| F{Authorized?}
    F -->|No| G[403 Forbidden]
    F -->|Yes| H{Resource Exists?}
    H -->|No| I[404 Not Found]
    H -->|Yes| J{Valid Method?}
    J -->|No| K[405 Method Not Allowed]
    J -->|Yes| L{Rate Limit?}
    L -->|Exceeded| M[429 Too Many Requests]
    L -->|OK| N{Server Error?}
    N -->|Yes| O[500 Internal Server Error]
    N -->|No| P[200 OK]

    style C fill:#ffcccc
    style E fill:#ff9999
    style G fill:#ff6666
    style I fill:#ff3333
    style K fill:#ff0000
    style M fill:#cc0000
    style O fill:#990000
    style P fill:#00cc00
```

## Rate Limiting Strategy

```mermaid
graph LR
    subgraph "Rate Limits"
        L1[Auth: 10/min]
        L2[API: 100/min]
        L3[Upload: 20/hour]
    end

    subgraph "Implementation"
        I1[Redis Storage]
        I2[Sliding Window]
        I3[IP + User Key]
    end

    subgraph "Response Headers"
        H1[X-RateLimit-Limit]
        H2[X-RateLimit-Remaining]
        H3[X-RateLimit-Reset]
    end

    L1 --> I1
    L2 --> I1
    L3 --> I1
    I1 --> I2
    I2 --> I3
    I3 --> H1
    I3 --> H2
    I3 --> H3

    style L1 fill:#e1f5ff
    style L2 fill:#e1f5ff
    style L3 fill:#e1f5ff
    style I1 fill:#ffe1e1
    style I2 fill:#ffcccc
    style I3 fill:#ffb3b3
```
