# Users API

## Overview

The Users API manages user accounts, roles, permissions, and user-related operations.

## Base URL

```
Development: http://localhost:8000/api/users
Production: https://api.claimforge.com/api/users
```

## Authentication

All endpoints require authentication via JWT token:

```
Authorization: Bearer <token>
```

## Endpoints

### Get All Users

Retrieve a list of users with optional filtering and pagination.

**Endpoint:** `GET /api/users`

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| page | number | No | Page number (default: 1) |
| limit | number | No | Items per page (default: 20, max: 100) |
| role | string | No | Filter by role |
| search | string | No | Search by name or email |
| isActive | boolean | No | Filter by active status |
| sort | string | No | Sort field (default: createdAt) |
| order | string | No | Sort order: asc or desc (default: desc) |

**Example Request:**

```
GET /api/users?role=adjuster&page=1&limit=20
```

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "adjuster@example.com",
      "firstName": "Jane",
      "lastName": "Smith",
      "phone": "+1234567890",
      "roles": ["adjuster"],
      "isActive": true,
      "isEmailVerified": true,
      "createdAt": "2024-01-15T10:30:00Z",
      "lastLogin": "2024-01-20T14:45:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 50,
    "totalPages": 3
  }
}
```

**Permissions Required:** `read:users` (admin, manager)

---

### Get User by ID

Retrieve detailed information about a specific user.

**Endpoint:** `GET /api/users/:id`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "email": "adjuster@example.com",
  "firstName": "Jane",
  "lastName": "Smith",
  "phone": "+1234567890",
  "roles": ["adjuster"],
  "permissions": [
    "read:claim",
    "update:claim",
    "assign:claim",
    "read:document",
    "upload:document"
  ],
  "isActive": true,
  "isEmailVerified": true,
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-18T11:20:00Z",
  "lastLogin": "2024-01-20T14:45:00Z"
}
```

**Error Responses:**

- `404 Not Found`: User not found
- `403 Forbidden`: Insufficient permissions

---

### Update User

Update user details (partial update supported).

**Endpoint:** `PUT /api/users/:id`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Request Body:**

```json
{
  "firstName": "Jane",
  "lastName": "Doe-Smith",
  "phone": "+1987654321"
}
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "firstName": "Jane",
  "lastName": "Doe-Smith",
  "phone": "+1987654321",
  "updatedAt": "2024-01-21T09:30:00Z"
}
```

**Error Responses:**

- `400 Bad Request`: Invalid input data
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: User not found

**Permissions Required:** `update:user` (admin, manager, or own user)

---

### Deactivate User

Deactivate a user account (soft delete).

**Endpoint:** `POST /api/users/:id/deactivate`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Request Body:**

```json
{
  "reason": "Employee termination"
}
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "isActive": false,
  "deactivatedAt": "2024-01-21T10:00:00Z",
  "deactivationReason": "Employee termination"
}
```

**Error Responses:**

- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: User not found

**Permissions Required:** `deactivate:user` (admin)

---

### Activate User

Reactivate a deactivated user account.

**Endpoint:** `POST /api/users/:id/activate`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "isActive": true,
  "activatedAt": "2024-01-21T11:00:00Z"
}
```

**Error Responses:**

- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: User not found

**Permissions Required:** `activate:user` (admin)

---

### Assign Role

Assign a role to a user.

**Endpoint:** `POST /api/users/:id/roles`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Request Body:**

```json
{
  "role": "adjuster",
  "expiresAt": "2024-12-31T23:59:59Z"
}
```

**Response (201 Created):**

```json
{
  "id": "role-assignment-id",
  "userId": "550e8400-e29b-41d4-a716-446655440000",
  "role": "adjuster",
  "assignedAt": "2024-01-21T12:00:00Z",
  "expiresAt": "2024-12-31T23:59:59Z"
}
```

**Error Responses:**

- `400 Bad Request`: Invalid role
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: User not found

**Permissions Required:** `assign:role` (admin)

---

### Remove Role

Remove a role from a user.

**Endpoint:** `DELETE /api/users/:id/roles/:roleId`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |
| roleId | string | Yes | Role assignment UUID |

**Response (204 No Content)**

**Error Responses:**

- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: User or role assignment not found

**Permissions Required:** `remove:role` (admin)

---

### Get User Roles

Retrieve all roles assigned to a user.

**Endpoint:** `GET /api/users/:id/roles`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "role-assignment-id-1",
      "role": "adjuster",
      "roleDetails": {
        "name": "Adjuster",
        "description": "Claims adjuster with investigation permissions"
      },
      "assignedAt": "2024-01-15T10:30:00Z",
      "expiresAt": null
    },
    {
      "id": "role-assignment-id-2",
      "role": "reviewer",
      "roleDetails": {
        "name": "Reviewer",
        "description": "Claims reviewer with approval permissions"
      },
      "assignedAt": "2024-01-20T14:00:00Z",
      "expiresAt": "2024-06-30T23:59:59Z"
    }
  ]
}
```

---

### Get User Permissions

Retrieve all permissions for a user (inherited from roles).

**Endpoint:** `GET /api/users/:id/permissions`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| id | string | Yes | User UUID |

**Response (200 OK):**

```json
{
  "data": [
    {
      "name": "read:claim",
      "resource": "claim",
      "action": "read",
      "description": "View claim details"
    },
    {
      "name": "update:claim",
      "resource": "claim",
      "action": "update",
      "description": "Update claim information"
    },
    {
      "name": "assign:claim",
      "resource": "claim",
      "action": "assign",
      "description": "Assign claims to adjusters"
    }
  ]
}
```

---

### Get All Roles

Retrieve all available roles in the system.

**Endpoint:** `GET /api/roles`

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "role-id-1",
      "name": "claimant",
      "description": "Regular user who can file claims",
      "permissions": ["create:claim", "read:own:claim"]
    },
    {
      "id": "role-id-2",
      "name": "adjuster",
      "description": "Claims adjuster with investigation permissions",
      "permissions": [
        "read:claim",
        "update:claim",
        "assign:claim",
        "read:document",
        "upload:document"
      ]
    },
    {
      "id": "role-id-3",
      "name": "reviewer",
      "description": "Claims reviewer with approval permissions",
      "permissions": [
        "read:claim",
        "approve:claim",
        "deny:claim",
        "settle:claim"
      ]
    },
    {
      "id": "role-id-4",
      "name": "manager",
      "description": "Team manager with oversight permissions",
      "permissions": [
        "read:all:claim",
        "assign:claim",
        "approve:claim",
        "manage:users",
        "view:analytics"
      ]
    },
    {
      "id": "role-id-5",
      "name": "admin",
      "description": "System administrator with full access",
      "permissions": ["*"]
    }
  ]
}
```

**Permissions Required:** `read:roles` (admin, manager)

---

### Get Role Permissions

Retrieve all permissions for a specific role.

**Endpoint:** `GET /api/roles/:roleId/permissions`

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| roleId | string | Yes | Role UUID |

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "perm-id-1",
      "name": "read:claim",
      "resource": "claim",
      "action": "read",
      "description": "View claim details"
    },
    {
      "id": "perm-id-2",
      "name": "update:claim",
      "resource": "claim",
      "action": "update",
      "description": "Update claim information"
    }
  ]
}
```

---

### Get All Permissions

Retrieve all available permissions in the system.

**Endpoint:** `GET /api/permissions`

**Response (200 OK):**

```json
{
  "data": [
    {
      "id": "perm-id-1",
      "name": "create:claim",
      "resource": "claim",
      "action": "create",
      "description": "Create new claims"
    },
    {
      "id": "perm-id-2",
      "name": "read:claim",
      "resource": "claim",
      "action": "read",
      "description": "View claim details"
    },
    {
      "id": "perm-id-3",
      "name": "update:claim",
      "resource": "claim",
      "action": "update",
      "description": "Update claim information"
    },
    {
      "id": "perm-id-4",
      "name": "delete:claim",
      "resource": "claim",
      "action": "delete",
      "description": "Delete claims"
    }
  ]
}
```

**Permissions Required:** `read:permissions` (admin)

---

## Data Models

### User

```typescript
interface User {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  phone?: string;
  roles: string[];
  permissions: string[];
  isActive: boolean;
  isEmailVerified: boolean;
  createdAt: string;
  updatedAt: string;
  lastLogin?: string;
}
```

### Role

```typescript
interface Role {
  id: string;
  name: string;
  description: string;
  permissions: string[];
}
```

### Permission

```typescript
interface Permission {
  id: string;
  name: string;
  resource: string;
  action: string;
  description: string;
}
```

### RoleAssignment

```typescript
interface RoleAssignment {
  id: string;
  userId: string;
  role: string;
  assignedAt: string;
  expiresAt?: string;
}
```

---

## Role Hierarchy

Roles are hierarchical in terms of permissions:

```
admin
  ├─ All permissions (*)
  └─ Can manage all users and system settings

manager
  ├─ read:all:claim
  ├─ assign:claim
  ├─ approve:claim
  ├─ manage:users (limited)
  └─ view:analytics

reviewer
  ├─ read:claim
  ├─ approve:claim
  ├─ deny:claim
  └─ settle:claim

adjuster
  ├─ read:claim
  ├─ update:claim
  ├─ assign:claim
  ├─ read:document
  └─ upload:document

claimant
  ├─ create:claim
  └─ read:own:claim
```

---

## Examples

### User Management Flow

```bash
# 1. Get all users
curl -X GET "http://localhost:8000/api/users?page=1&limit=20" \
  -H "Authorization: Bearer $TOKEN"

# 2. Assign a role to a user
curl -X POST http://localhost:8000/api/users/user-id-123/roles \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "role": "adjuster"
  }'

# 3. Get user permissions
curl -X GET http://localhost:8000/api/users/user-id-123/permissions \
  -H "Authorization: Bearer $TOKEN"

# 4. Update user information
curl -X PUT http://localhost:8000/api/users/user-id-123 \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Jane",
    "lastName": "Doe-Smith",
    "phone": "+1987654321"
  }'

# 5. Deactivate user
curl -X POST http://localhost:8000/api/users/user-id-123/deactivate \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "reason": "Employee termination"
  }'
```

### Role Assignment with Expiration

```bash
# Assign temporary role
curl -X POST http://localhost:8000/api/users/user-id-123/roles \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "role": "reviewer",
    "expiresAt": "2024-12-31T23:59:59Z"
  }'
```
