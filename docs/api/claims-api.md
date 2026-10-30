# Claims API

## Overview

The Claims API manages the complete lifecycle of insurance claims, including creation, updates, status changes, and settlements.

## Base URL

```
Development: http://localhost:8000/api/claims
Production: https://api.claimforge.com/api/claims
```

## Authentication

All endpoints require authentication via JWT token:

```
Authorization: Bearer <token>
```

## Endpoints

### Get All Claims

Retrieve a list of claims with optional filtering and pagination.

**Endpoint:** `GET /api/claims`

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| page | number | No | Page number (default: 1) |
| limit | number | No | Items per page (default: 20, max: 100) |
| status | string | No | Filter by status |
| userId | string | No | Filter by user ID |
| assignedTo | string | No | Filter by assignee ID |
| dateFrom | string | No | Filter by filed date (ISO 8601) |
| dateTo | string | No | Filter by filed date (ISO 8601) |
| sort | string | No | Sort field (default: createdAt) |
| order | string | No | Sort order: asc or desc (default: desc) |

**Example Request:**

```
GET /api/claims?status=under_review&page=1&limit=20
```

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "claimNumber": "CLM-1A2B3C-XYZ",
      "userId": "user-id-123",
      "assignedTo": "adjuster-id-456",
      "status": "under_review",
      "claimAmount": 5000.00,
      "settlementAmount": null,
      "policyNumber": "POL-12345",
      "incidentDate": "2024-01-10",
      "filedDate": "2024-01-15",
      "description": "Vehicle damage from accident",
      "createdAt": "2024-01-15T10:30:00Z",
      "updatedAt": "2024-01-16T14:20:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

---

### Get Claim by ID

Retrieve detailed information about a specific claim.

**Endpoint:** `GET /api/claims/:id`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "claimNumber": "CLM-1A2B3C-XYZ",
  "userId": "user-id-123",
  "user": {
    "id": "user-id-123",
    "email": "claimant@example.com",
    "firstName": "John",
    "lastName": "Doe"
  },
  "assignedTo": "adjuster-id-456",
  "assignee": {
    "id": "adjuster-id-456",
    "email": "adjuster@example.com",
    "firstName": "Jane",
    "lastName": "Smith"
  },
  "status": "under_review",
  "claimAmount": 5000.00,
  "settlementAmount": null,
  "policyNumber": "POL-12345",
  "incidentDate": "2024-01-10",
  "filedDate": "2024-01-15",
  "closedDate": null,
  "description": "Vehicle damage from accident",
  "metadata": {
    "vehicleMake": "Toyota",
    "vehicleModel": "Camry",
    "vehicleYear": 2020
  },
  "documents": [
    {
      "id": "doc-id-789",
      "documentType": "police_report",
      "fileName": "police_report.pdf",
      "fileUrl": "https://s3.amazonaws.com/...",
      "uploadedAt": "2024-01-15T11:00:00Z"
    }
  ],
  "notes": [
    {
      "id": "note-id-abc",
      "authorId": "adjuster-id-456",
      "author": {
        "firstName": "Jane",
        "lastName": "Smith"
      },
      "content": "Initial review complete. Needs more documentation.",
      "isInternal": true,
      "createdAt": "2024-01-16T09:30:00Z"
    }
  ],
  "history": [
    {
      "id": "history-id-123",
      "oldStatus": "submitted",
      "newStatus": "under_review",
      "changedBy": "adjuster-id-456",
      "changeReason": "Assigned for review",
      "changedAt": "2024-01-16T08:00:00Z"
    }
  ],
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-16T14:20:00Z"
}
```

**Error Responses:**

- `404 Not Found`: Claim not found
- `403 Forbidden`: Insufficient permissions

---

### Create Claim

Submit a new insurance claim.

**Endpoint:** `POST /api/claims`

**Request Body:**

```json
{
  "policyNumber": "POL-12345",
  "incidentDate": "2024-01-10",
  "claimAmount": 5000.00,
  "description": "Vehicle damage from accident on highway",
  "metadata": {
    "vehicleMake": "Toyota",
    "vehicleModel": "Camry",
    "vehicleYear": 2020,
    "location": "I-95 North, Mile 45"
  }
}
```

**Response (201 Created):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "claimNumber": "CLM-1A2B3C-XYZ",
  "userId": "user-id-123",
  "status": "submitted",
  "claimAmount": 5000.00,
  "policyNumber": "POL-12345",
  "incidentDate": "2024-01-10",
  "filedDate": "2024-01-15",
  "description": "Vehicle damage from accident on highway",
  "metadata": {
    "vehicleMake": "Toyota",
    "vehicleModel": "Camry",
    "vehicleYear": 2020,
    "location": "I-95 North, Mile 45"
  },
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-15T10:30:00Z"
}
```

**Error Responses:**

- `400 Bad Request`: Invalid input data
- `409 Conflict`: Duplicate claim for same incident

---

### Update Claim

Update claim details (partial update supported).

**Endpoint:** `PUT /api/claims/:id`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Request Body:**

```json
{
  "status": "investigating",
  "description": "Updated description with additional details",
  "metadata": {
    "witnesses": 2,
    "policeReportNumber": "PR-2024-001"
  }
}
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "claimNumber": "CLM-1A2B3C-XYZ",
  "status": "investigating",
  "description": "Updated description with additional details",
  "updatedAt": "2024-01-16T15:30:00Z"
}
```

**Error Responses:**

- `400 Bad Request`: Invalid input data
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Claim not found

---

### Update Claim Status

Change the status of a claim with reason.

**Endpoint:** `POST /api/claims/:id/status`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Request Body:**

```json
{
  "status": "approved",
  "reason": "All documentation received and verified"
}
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "claimNumber": "CLM-1A2B3C-XYZ",
  "status": "approved",
  "history": [
    {
      "oldStatus": "investigating",
      "newStatus": "approved",
      "changedBy": "user-id-123",
      "changeReason": "All documentation received and verified",
      "changedAt": "2024-01-16T16:00:00Z"
    }
  ]
}
```

**Error Responses:**

- `400 Bad Request`: Invalid status transition
- `403 Forbidden`: Insufficient permissions

---

### Assign Claim

Assign a claim to an adjuster or reviewer.

**Endpoint:** `POST /api/claims/:id/assign`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Request Body:**

```json
{
  "assignedTo": "adjuster-id-456",
  "priority": "high"
}
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "assignedTo": "adjuster-id-456",
  "priority": "high",
  "updatedAt": "2024-01-16T16:30:00Z"
}
```

**Error Responses:**

- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: User not found

---

### Set Claim Settlement

Set the settlement amount for an approved claim.

**Endpoint:** `POST /api/claims/:id/settlement`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Request Body:**

```json
{
  "settlementAmount": 4500.00,
  "reason": "Deductible applied"
}
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "settlementAmount": 4500.00,
  "status": "settled",
  "updatedAt": "2024-01-16T17:00:00Z"
}
```

**Error Responses:**

- `400 Bad Request`: Claim not in approved status
- `403 Forbidden`: Insufficient permissions

---

### Delete Claim

Delete a claim (soft delete, requires admin/manager role).

**Endpoint:** `DELETE /api/claims/:id`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Response (204 No Content)**

**Error Responses:**

- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Claim not found

---

### Get Claim Documents

Retrieve all documents associated with a claim.

**Endpoint:** `GET /api/claims/:id/documents`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "doc-id-789",
      "documentType": "police_report",
      "fileName": "police_report.pdf",
      "fileUrl": "https://s3.amazonaws.com/...",
      "fileSize": 1024000,
      "mimeType": "application/pdf",
      "uploadedAt": "2024-01-15T11:00:00Z",
      "uploadedBy": "user-id-123"
    }
  ]
}
```

---

### Add Claim Note

Add a note to a claim.

**Endpoint:** `POST /api/claims/:id/notes`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Request Body:**

```json
{
  "content": "Additional information needed from claimant",
  "isInternal": true
}
```

**Response (201 Created):**

```json
{
  "id": "note-id-abc",
  "claimId": "550e8400-e29b-41d4-a716-446655440000",
  "authorId": "user-id-123",
  "content": "Additional information needed from claimant",
  "isInternal": true,
  "createdAt": "2024-01-16T18:00:00Z"
}
```

---

### Get Claim History

Retrieve the full history of status changes for a claim.

**Endpoint:** `GET /api/claims/:id/history`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | Claim UUID |

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "history-id-123",
      "oldStatus": "submitted",
      "newStatus": "under_review",
      "changedBy": "adjuster-id-456",
      "changer": {
        "firstName": "Jane",
        "lastName": "Smith"
      },
      "changeReason": "Assigned for review",
      "changedAt": "2024-01-16T08:00:00Z"
    },
    {
      "id": "history-id-456",
      "oldStatus": "under_review",
      "newStatus": "investigating",
      "changedBy": "adjuster-id-456",
      "changer": {
        "firstName": "Jane",
        "lastName": "Smith"
      },
      "changeReason": "Additional documentation needed",
      "changedAt": "2024-01-16T14:00:00Z"
    }
  ]
}
```

---

## Data Models

### Claim

```typescript
interface Claim {
  id: string;
  claimNumber: string;
  userId: string;
  assignedTo?: string;
  status: ClaimStatus;
  claimAmount: number;
  settlementAmount?: number;
  policyNumber: string;
  incidentDate: string;
  filedDate: string;
  closedDate?: string;
  description: string;
  metadata?: Record<string, any>;
  createdAt: string;
  updatedAt: string;
}

type ClaimStatus =
  | 'submitted'
  | 'under_review'
  | 'investigating'
  | 'approved'
  | 'denied'
  | 'settled'
  | 'closed';
```

### CreateClaimDto

```typescript
interface CreateClaimDto {
  policyNumber: string;
  incidentDate: string;
  claimAmount: number;
  description: string;
  metadata?: Record<string, any>;
}
```

### UpdateClaimDto

```typescript
interface UpdateClaimDto {
  status?: ClaimStatus;
  description?: string;
  metadata?: Record<string, any>;
}
```

---

## Status Transitions

Claims can only transition between certain statuses:

```
submitted → under_review → investigating → approved → settled → closed
                                    ↓
                                  denied → closed
```

Some transitions require specific roles:

| Transition | Required Role |
|------------|---------------|
| submitted → under_review | adjuster, manager, admin |
| under_review → investigating | adjuster, manager, admin |
| investigating → approved | reviewer, manager, admin |
| investigating → denied | reviewer, manager, admin |
| approved → settled | reviewer, manager, admin |
| settled/denied → closed | adjuster, manager, admin |

---

## Examples

### Create and Track a Claim

```bash
# 1. Create a claim
CLAIM_RESPONSE=$(curl -X POST http://localhost:8000/api/claims \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "policyNumber": "POL-12345",
    "incidentDate": "2024-01-10",
    "claimAmount": 5000.00,
    "description": "Vehicle damage from accident"
  }')

CLAIM_ID=$(echo $CLAIM_RESPONSE | jq -r '.id')

# 2. Upload a document
curl -X POST http://localhost:8000/api/documents \
  -H "Authorization: Bearer $TOKEN" \
  -F "file=@police_report.pdf" \
  -F "claimId=$CLAIM_ID" \
  -F "documentType=police_report"

# 3. Check claim status
curl -X GET http://localhost:8000/api/claims/$CLAIM_ID \
  -H "Authorization: Bearer $TOKEN"

# 4. Add a note
curl -X POST http://localhost:8000/api/claims/$CLAIM_ID/notes \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Police report uploaded",
    "isInternal": false
  }'
```

### Filter Claims

```bash
# Get all claims in review
curl -X GET "http://localhost:8000/api/claims?status=under_review" \
  -H "Authorization: Bearer $TOKEN"

# Get claims by date range
curl -X GET "http://localhost:8000/api/claims?dateFrom=2024-01-01&dateTo=2024-01-31" \
  -H "Authorization: Bearer $TOKEN"

# Get claims assigned to specific adjuster
curl -X GET "http://localhost:8000/api/claims?assignedTo=adjuster-id-456" \
  -H "Authorization: Bearer $TOKEN"
```
