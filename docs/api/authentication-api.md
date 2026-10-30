# Authentication API

## Overview

The Authentication API handles user registration, login, logout, and token management using JWT (JSON Web Tokens).

## Base URL

```
Development: http://localhost:8000/api/auth
Production: https://api.claimforge.com/api/auth
```

## Authentication

Most endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <token>
```

## Endpoints

### Register User

Register a new user account.

**Endpoint:** `POST /api/auth/register`

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1234567890"
}
```

**Response (201 Created):**

```json
{
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+1234567890",
    "roles": ["claimant"],
    "createdAt": "2024-01-15T10:30:00Z"
  },
  "tokens": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600
  }
}
```

**Error Responses:**

- `400 Bad Request`: Invalid input data
- `409 Conflict`: Email already exists

---

### Login

Authenticate a user and receive tokens.

**Endpoint:** `POST /api/auth/login`

**Request Body:**

```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}
```

**Response (200 OK):**

```json
{
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "roles": ["claimant"],
    "lastLogin": "2024-01-15T10:30:00Z"
  },
  "tokens": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600
  }
}
```

**Error Responses:**

- `401 Unauthorized`: Invalid credentials
- `429 Too Many Requests`: Rate limit exceeded

---

### Logout

Invalidate the current session.

**Endpoint:** `POST /api/auth/logout`

**Headers:**

```
Authorization: Bearer <token>
```

**Response (200 OK):**

```json
{
  "message": "Successfully logged out"
}
```

---

### Refresh Token

Refresh an expired access token using a refresh token.

**Endpoint:** `POST /api/auth/refresh`

**Request Body:**

```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response (200 OK):**

```json
{
  "tokens": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600
  }
}
```

**Error Responses:**

- `401 Unauthorized`: Invalid or expired refresh token

---

### Forgot Password

Initiate password reset process.

**Endpoint:** `POST /api/auth/forgot-password`

**Request Body:**

```json
{
  "email": "user@example.com"
}
```

**Response (200 OK):**

```json
{
  "message": "Password reset email sent if email exists"
}
```

**Note:** Always returns success to prevent email enumeration.

---

### Reset Password

Reset password using a reset token.

**Endpoint:** `POST /api/auth/reset-password`

**Request Body:**

```json
{
  "token": "reset_token_here",
  "newPassword": "NewSecurePassword123!"
}
```

**Response (200 OK):**

```json
{
  "message": "Password successfully reset"
}
```

**Error Responses:**

- `400 Bad Request`: Invalid or expired token
- `400 Bad Request`: Password doesn't meet requirements

---

### Change Password

Change password for authenticated user.

**Endpoint:** `POST /api/auth/change-password`

**Headers:**

```
Authorization: Bearer <token>
```

**Request Body:**

```json
{
  "currentPassword": "OldPassword123!",
  "newPassword": "NewSecurePassword123!"
}
```

**Response (200 OK):**

```json
{
  "message": "Password successfully changed"
}
```

**Error Responses:**

- `400 Bad Request`: Current password is incorrect
- `400 Bad Request`: New password doesn't meet requirements

---

### Verify Email

Verify user email address.

**Endpoint:** `POST /api/auth/verify-email`

**Request Body:**

```json
{
  "token": "verification_token_here"
}
```

**Response (200 OK):**

```json
{
  "message": "Email successfully verified"
}
```

**Error Responses:**

- `400 Bad Request`: Invalid or expired token

---

### Get Current User

Get current authenticated user details.

**Endpoint:** `GET /api/auth/me`

**Headers:**

```
Authorization: Bearer <token>
```

**Response (200 OK):**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1234567890",
  "roles": ["claimant"],
  "permissions": ["create:claim", "read:claim"],
  "isEmailVerified": true,
  "createdAt": "2024-01-15T10:30:00Z",
  "lastLogin": "2024-01-20T14:45:00Z"
}
```

**Error Responses:**

- `401 Unauthorized`: Invalid or expired token

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
  isEmailVerified: boolean;
  isActive: boolean;
  createdAt: string;
  updatedAt: string;
  lastLogin?: string;
}
```

### Tokens

```typescript
interface Tokens {
  accessToken: string;
  refreshToken: string;
  expiresIn: number; // seconds
}
```

---

## Error Response Format

All error responses follow this format:

```json
{
  "error": "Error message",
  "code": "ERROR_CODE",
  "details": {
    // Additional error details
  }
}
```

---

## Rate Limiting

- Login endpoint: 5 requests per 15 minutes per IP
- Register endpoint: 3 requests per hour per IP
- Other endpoints: 100 requests per 15 minutes per user

Rate limit headers are included in responses:

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705789200
```

---

## Security Considerations

1. **Password Requirements:**
   - Minimum 8 characters
   - At least one uppercase letter
   - At least one lowercase letter
   - At least one number
   - At least one special character

2. **Token Security:**
   - Access tokens expire in 1 hour
   - Refresh tokens expire in 7 days
   - Refresh tokens are rotated on each use
   - Tokens are invalidated on logout

3. **Account Lockout:**
   - 5 failed login attempts triggers temporary lockout
   - Lockout duration: 15 minutes
   - Manual unlock by admin required for repeated failures

4. **Email Verification:**
   - Required for full account access
   - Verification link expires in 24 hours
   - Can request new verification link

---

## Examples

### Complete Registration Flow

```bash
# 1. Register
curl -X POST http://localhost:8000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecurePassword123!",
    "firstName": "John",
    "lastName": "Doe"
  }'

# 2. Verify email (using token from email)
curl -X POST http://localhost:8000/api/auth/verify-email \
  -H "Content-Type: application/json" \
  -d '{"token": "verification_token"}'

# 3. Login
curl -X POST http://localhost:8000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecurePassword123!"
  }'
```

### Using Refresh Token

```bash
# When access token expires, use refresh token
curl -X POST http://localhost:8000/api/auth/refresh \
  -H "Content-Type: application/json" \
  -d '{"refreshToken": "your_refresh_token"}'
```
