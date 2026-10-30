# Claims Management Workflow Diagrams

## Claims Lifecycle Flow

```mermaid
stateDiagram-v2
    [*] --> ClaimIntake: New Claim Filed
    ClaimIntake --> Triage: Initial Review
    Triage --> Investigation: Needs Investigation
    Triage --> AutoSettlement: Simple Case
    Investigation --> Review: Evidence Collected
    Review --> Negotiation: Additional Info Needed
    Review --> Settlement: Ready for Settlement
    Negotiation --> Review: Info Received
    Negotiation --> Escalation: Dispute
    Settlement --> Payment: Approved
    Payment --> Closed: Paid
    Escalation --> ManagementReview: Management Intervention
    ManagementReview --> Settlement: Decision Made
    ManagementReview --> Rejection: Claim Denied
    Rejection --> Closed: Final Status
    AutoSettlement --> Payment: Auto-Approved
    Closed --> [*]

    note right of ClaimIntake
        User submits claim
        with basic info
    end note

    note right of Investigation
        Assign adjuster
        Collect documents
        Interview parties
    end note

    note right of Settlement
        Calculate amount
        Get approval
        Notify parties
    end note
```

## Detailed Claims Process

```mermaid
flowchart TD
    Start([Start]) --> Submit[Submit Claim]
    Submit --> Validate{Valid?}
    Validate -->|No| Return[Return to User]
    Validate -->|Yes| Assign[Assign to Adjuster]
    Assign --> Notify[Notify Stakeholders]
    Notify --> Collect[Collect Evidence]
    Collect --> Complete{Evidence Complete?}
    Complete -->|No| Request[Request More Info]
    Request --> Collect
    Complete -->|Yes| Assess[Assess Claim]
    Assess --> Decision{Decision}
    Decision -->|Approve| Calculate[Calculate Settlement]
    Decision -->|Deny| DenyReason[Provide Reason]
    Decision -->|Investigate| DeepDive[Deep Investigation]
    DeepDive --> Assess
    Calculate --> Approval{Approval Needed?}
    Approval -->|Yes| Manager[Manager Review]
    Approval -->|No| Payment[Process Payment]
    Manager --> FinalDecision{Final Decision}
    FinalDecision -->|Approve| Payment
    FinalDecision -->|Deny| DenyReason
    Payment --> NotifyPay[Notify Payment]
    DenyReason --> NotifyDeny[Notify Denial]
    NotifyPay --> Archive[Archive Claim]
    NotifyDeny --> Archive
    Archive --> End([End])

    style Start fill:#e1f5ff
    style End fill:#e1ffe1
    style Submit fill:#fff4e1
    style Validate fill:#ffe1e1
    style Assign fill:#ffcccc
    style Notify fill:#ffb3b3
    style Collect fill:#ff9999
    style Assess fill:#ff6666
    style Calculate fill:#e1ffe1
    style Payment fill:#ccffcc
    style Archive fill:#99ff99
```

## Role-Based Workflows

```mermaid
graph TB
    subgraph "Claimant Workflow"
        A1[File Claim]
        A2[Upload Documents]
        A3[Track Status]
        A4[Respond to Queries]
        A5[Receive Settlement]
    end

    subgraph "Adjuster Workflow"
        B1[Review New Claims]
        B2[Assign Priority]
        B3[Investigate]
        B4[Request Information]
        B5[Make Recommendation]
    end

    subgraph "Reviewer Workflow"
        C1[Review Recommendations]
        C2[Verify Calculations]
        C3[Approve/Deny]
        C4[Escalate if Needed]
    end

    subgraph "Manager Workflow"
        D1[Monitor Metrics]
        D2[Handle Escalations]
        D3[Approve Large Claims]
        D4[Generate Reports]
    end

    A1 --> B1
    A2 --> B3
    A3 --> B2
    A4 --> B4
    B5 --> C1
    C3 --> A5
    C4 --> D2
    D3 --> C3

    style A1 fill:#e1f5ff
    style A2 fill:#e1f5ff
    style A3 fill:#e1f5ff
    style A4 fill:#e1f5ff
    style A5 fill:#e1f5ff
    style B1 fill:#ffe1e1
    style B2 fill:#ffe1e1
    style B3 fill:#ffe1e1
    style B4 fill:#ffe1e1
    style B5 fill:#ffe1e1
    style C1 fill:#e1ffe1
    style C2 fill:#e1ffe1
    style C3 fill:#e1ffe1
    style C4 fill:#e1ffe1
    style D1 fill:#fff4e1
    style D2 fill:#fff4e1
    style D3 fill:#fff4e1
    style D4 fill:#fff4e1
```

## Document Management Flow

```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant Storage
    participant MongoDB
    participant Audit

    User->>Frontend: Upload Document
    Frontend->>API: POST /api/documents
    API->>API: Validate File
    API->>Storage: Upload to S3/Blob
    Storage-->>API: File URL
    API->>MongoDB: Store Metadata
    API->>Audit: Log Upload
    API-->>Frontend: Document ID
    Frontend-->>User: Upload Success

    User->>Frontend: View Document
    Frontend->>API: GET /api/documents/:id
    API->>MongoDB: Retrieve Metadata
    API->>Storage: Get Presigned URL
    Storage-->>API: Temporary URL
    API-->>Frontend: Document URL
    Frontend-->>User: Display Document
```

## Settlement Calculation Flow

```mermaid
flowchart LR
    A[Claim Amount] --> B[Policy Limits]
    B --> C{Within Limits?}
    C -->|Yes| D[Apply Deductible]
    C -->|No| E[Cap at Limit]
    E --> D
    D --> F[Calculate Coverage]
    F --> G[Apply Discounts]
    G --> H[Final Settlement]
    H --> I[Approval Check]
    I --> J{Approval Needed?}
    J -->|Yes| K[Manager Approval]
    J -->|No| L[Process Payment]
    K --> M{Approved?}
    M -->|Yes| L
    M -->|No| N[Notify Adjuster]
    N --> F

    style A fill:#e1f5ff
    style B fill:#ffe1e1
    style C fill:#ffcccc
    style D fill:#ffb3b3
    style E fill:#ff9999
    style F fill:#ff6666
    style G fill:#e1ffe1
    style H fill:#ccffcc
    style I fill:#99ff99
    style J fill:#66ff66
    style K fill:#33ff33
    style L fill:#e1f5ff
    style M fill:#ffe1e1
    style N fill:#ffcccc
```

## Notification Flow

```mermaid
graph TB
    subgraph "Claim Events"
        E1[Claim Filed]
        E2[Status Change]
        E3[Document Request]
        E4[Settlement Offer]
        E5[Payment Sent]
    end

    subgraph "Notifications"
        N1[Email]
        N2[SMS]
        N3[In-App]
        N4[Push]
    end

    subgraph "Recipients"
        R1[Claimant]
        R2[Adjuster]
        R3[Reviewer]
        R4[Manager]
    end

    E1 --> N1
    E1 --> N3
    E2 --> N1
    E2 --> N2
    E3 --> N1
    E3 --> N2
    E4 --> N1
    E4 --> N2
    E5 --> N1
    E5 --> N2

    N1 --> R1
    N2 --> R1
    N3 --> R2
    N4 --> R3

    style E1 fill:#e1f5ff
    style E2 fill:#e1f5ff
    style E3 fill:#e1f5ff
    style E4 fill:#e1f5ff
    style E5 fill:#e1f5ff
    style N1 fill:#ffe1e1
    style N2 fill:#ffcccc
    style N3 fill:#ffb3b3
    style N4 fill:#ff9999
    style R1 fill:#e1ffe1
    style R2 fill:#ccffcc
    style R3 fill:#99ff99
    style R4 fill:#66ff66
```
