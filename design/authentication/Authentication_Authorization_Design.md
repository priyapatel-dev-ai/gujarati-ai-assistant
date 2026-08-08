# Authentication & Authorization Design

## 1. Purpose

Define how users register, log in, receive JWT tokens, and access protected resources in the Gujarati AI Writing Assistant.

---

## 2. Authentication vs Authorization

### Authentication

Authentication answers:

> Who is the user?

Example:

```text
Email + Password
        ↓
Identity verified
        ↓
JWT issued
```

### Authorization

Authorization answers:

> What is this authenticated user allowed to access?

Example:

```text
User A
  ↓
Can access User A's documents

User B
  ↓
Cannot access User A's documents
```

---

## 3. Recommended Approach

Use:

- Django
- Django REST Framework
- JWT authentication
- PostgreSQL

JWT should be used for API authentication when the frontend is a separate application.

Recommended token library:

```text
djangorestframework-simplejwt
```

---

# 4. Registration Flow

```text
User enters:
- Name
- Email
- Password
        ↓
Frontend validation
        ↓
POST /api/v1/auth/register/
        ↓
Django validates data
        ↓
Check email uniqueness
        ↓
Hash password securely
        ↓
Create user
        ↓
Return success
```

The system must never store plaintext passwords.

---

# 5. Login Flow

```text
User enters email + password
        ↓
POST /api/v1/auth/login/
        ↓
Django verifies credentials
        ↓
Access Token + Refresh Token
        ↓
Frontend stores/handles tokens securely
        ↓
Authenticated API requests
```

Example response:

```json
{
  "access": "<access_token>",
  "refresh": "<refresh_token>"
}
```

---

# 6. JWT Tokens

## Access Token

Used for normal API requests.

Example:

```http
Authorization: Bearer <access_token>
```

Access tokens should have a relatively short lifetime.

## Refresh Token

Used to obtain a new access token after the access token expires.

The refresh token should have a longer lifetime and must be handled securely.

Exact expiry durations should be finalized during implementation based on security and product requirements.

---

# 7. Token Refresh Flow

```text
Access Token Expired
        ↓
Frontend sends Refresh Token
        ↓
POST /api/v1/auth/token/refresh/
        ↓
Backend validates Refresh Token
        ↓
New Access Token
        ↓
Continue API requests
```

---

# 8. Logout

JWT logout requires special handling because JWT access tokens are stateless.

Initial approach:

```text
User clicks Logout
        ↓
Frontend clears local authentication state
        ↓
Refresh token is invalidated/revoked where supported
        ↓
User returns to Login
```

For stronger security, implement refresh-token rotation and blacklisting.

Recommended library capability:

```text
Simple JWT Token Blacklist
```

This should be enabled when the authentication implementation is created.

---

# 9. Protected APIs

The following APIs should require authentication:

```text
/api/v1/documents/
/api/v1/grammar/check/
/api/v1/grammar/suggestions/
/api/v1/grammar/analyses/
/api/v1/ai/rewrite/
/api/v1/documents/{id}/download/
```

Public APIs should be explicitly identified rather than assuming an endpoint is public.

---

# 10. User Ownership

Every user-owned resource must be associated with the authenticated user.

Example:

```text
User A
  └── Document A

User B
  └── Document B
```

User A must not be able to retrieve, modify, analyze, or delete Document B.

The backend must verify ownership.

Never rely only on the frontend to enforce ownership.

---

# 11. Authorization Rules

### Normal User

Can:

- View own profile
- Create documents
- View own documents
- Update own documents
- Delete own documents
- Run grammar checks
- Use AI rewrite
- View own analysis history
- Download own documents

Cannot:

- View another user's documents
- Modify another user's documents
- Access administrative APIs

### Admin

Admin permissions will be defined separately if an admin panel or administration features are required.

---

# 12. Authentication APIs

## Register

```http
POST /api/v1/auth/register/
```

Request:

```json
{
  "email": "user@example.com",
  "password": "secure-password",
  "first_name": "User",
  "last_name": "Name"
}
```

---

## Login

```http
POST /api/v1/auth/login/
```

Request:

```json
{
  "email": "user@example.com",
  "password": "secure-password"
}
```

Response:

```json
{
  "access": "<access_token>",
  "refresh": "<refresh_token>"
}
```

---

## Refresh Token

```http
POST /api/v1/auth/token/refresh/
```

Request:

```json
{
  "refresh": "<refresh_token>"
}
```

---

## Current User

```http
GET /api/v1/auth/me/
```

Response example:

```json
{
  "id": "user-id",
  "email": "user@example.com",
  "first_name": "User",
  "last_name": "Name"
}
```

---

## Logout

```http
POST /api/v1/auth/logout/
```

Request may include the refresh token when token blacklisting is enabled.

---

# 13. Password Security

Passwords must:

- Never be stored as plaintext
- Be hashed using Django's password hashing system
- Meet configurable password-strength requirements
- Never be returned in API responses
- Never be logged

Password reset functionality should use a secure time-limited mechanism.

---

# 14. Login Security

The implementation should consider:

- Rate limiting login attempts
- Generic invalid-credential messages
- Secure HTTPS in production
- Secure token handling
- Password reset protection
- Account activation/verification if required

Do not reveal whether an email address exists when that would create a security/privacy risk.

---

# 15. Unauthorized Responses

If authentication is missing or invalid:

```http
401 Unauthorized
```

Example:

```json
{
  "error": {
    "code": "AUTHENTICATION_REQUIRED",
    "message": "Authentication is required."
  }
}
```

---

# 16. Forbidden Responses

If the user is authenticated but does not have permission:

```http
403 Forbidden
```

Example:

```json
{
  "error": {
    "code": "PERMISSION_DENIED",
    "message": "You do not have permission to access this resource."
  }
}
```

---

# 17. Frontend Authentication Flow

```text
Login Screen
    ↓
POST /auth/login/
    ↓
Receive JWT tokens
    ↓
Authenticated application
    ↓
API requests include access token
    ↓
Access token expires
    ↓
Refresh token
    ↓
Continue session
```

---

# 18. Security Principles

1. Authentication must be enforced on the backend.
2. Never trust user IDs supplied by the frontend for ownership checks.
3. Use HTTPS in production.
4. Keep JWT secrets on the server.
5. Never expose AI provider API keys to the frontend.
6. Validate all authentication inputs.
7. Rate-limit sensitive endpoints.
8. Use secure password hashing.
9. Keep access-token lifetime relatively short.
10. Use refresh-token rotation/blacklisting where appropriate.
11. Do not log passwords or tokens.
12. Return generic authentication errors where appropriate.

---

# 19. First Release Scope

Implement:

```text
POST /api/v1/auth/register/
POST /api/v1/auth/login/
POST /api/v1/auth/token/refresh/
GET  /api/v1/auth/me/
POST /api/v1/auth/logout/
```

Use Django REST Framework + Simple JWT.

Document ownership and protected APIs must be enforced from the beginning.

---

# 20. Future Authentication Features

Potential future features:

- Email verification
- Forgot password
- Reset password
- Google login
- Microsoft login
- Two-factor authentication
- Session/device management
- Account deletion
- Admin roles
- Subscription-based permissions

These should be implemented only after their requirements are finalized.
