# Grammar Checker - API Design

## 1. Purpose

Define the API contract between the frontend and Django REST Framework backend for the Gujarati AI Writing Assistant.

The API design should be stable enough for frontend development while allowing the backend implementation to evolve.

---

## 2. API Base URL

Development:

```text
http://127.0.0.1:8000/api/
```

Future production:

```text
https://<production-domain>/api/
```

API versioning:

```text
/api/v1/
```

Recommended final structure:

```text
/api/v1/grammar/
/api/v1/documents/
/api/v1/ai/
```

---

## 3. Authentication

Protected APIs should require authentication.

Recommended approach:

- JWT authentication
- Access token for normal API requests
- Refresh token for obtaining a new access token

Authentication implementation will be finalized in the separate Authentication and Authorization design.

Example header:

```http
Authorization: Bearer <access_token>
```

---

# 4. Grammar APIs

## 4.1 Check Grammar

### Endpoint

```http
POST /api/v1/grammar/check/
```

### Purpose

Analyze Gujarati text for grammar, spelling, sentence improvement, and AI suggestions.

### Request

```json
{
  "text": "Gujarati text goes here",
  "language": "gu",
  "document_id": null
}
```

### Response

```json
{
  "analysis_id": "analysis-id",
  "status": "completed",
  "language": "gu",
  "suggestions": [
    {
      "id": "suggestion-id",
      "type": "grammar",
      "original_text": "original",
      "suggested_text": "corrected",
      "explanation": "Explanation for the correction",
      "start_position": 10,
      "end_position": 18,
      "status": "pending"
    }
  ]
}
```

---

## 4.2 Update Suggestion Status

### Endpoint

```http
PATCH /api/v1/grammar/suggestions/{suggestion_id}/
```

### Purpose

Accept, reject, or ignore a suggestion.

### Request

```json
{
  "status": "accepted"
}
```

Allowed values:

```text
accepted
rejected
ignored
```

### Response

```json
{
  "id": "suggestion-id",
  "status": "accepted"
}
```

---

# 5. AI Rewrite APIs

## 5.1 Rewrite Text

### Endpoint

```http
POST /api/v1/ai/rewrite/
```

### Purpose

Generate an improved version of selected Gujarati text.

### Request

```json
{
  "text": "Gujarati text goes here",
  "rewrite_type": "professional",
  "language": "gu",
  "document_id": null
}
```

Possible rewrite types:

```text
simple
professional
formal
improve
shorten
expand
```

### Response

```json
{
  "rewrite_id": "rewrite-id",
  "status": "completed",
  "original_text": "Original text",
  "result_text": "AI generated text"
}
```

---

# 6. Document APIs

## 6.1 List Documents

### Endpoint

```http
GET /api/v1/documents/
```

Returns documents belonging to the authenticated user.

### Response

```json
{
  "results": [
    {
      "id": "document-id",
      "title": "My Gujarati Document",
      "language": "gu",
      "created_at": "2026-01-01T10:00:00Z",
      "updated_at": "2026-01-01T10:30:00Z"
    }
  ]
}
```

---

## 6.2 Create Document

### Endpoint

```http
POST /api/v1/documents/
```

### Request

```json
{
  "title": "My Gujarati Document",
  "content": "Gujarati content",
  "language": "gu"
}
```

### Response

```json
{
  "id": "document-id",
  "title": "My Gujarati Document",
  "content": "Gujarati content",
  "language": "gu",
  "created_at": "2026-01-01T10:00:00Z",
  "updated_at": "2026-01-01T10:00:00Z"
}
```

---

## 6.3 Get Document

### Endpoint

```http
GET /api/v1/documents/{document_id}/
```

---

## 6.4 Update Document

### Endpoint

```http
PUT /api/v1/documents/{document_id}/
```

### Request

```json
{
  "title": "Updated title",
  "content": "Updated Gujarati content",
  "language": "gu"
}
```

---

## 6.5 Delete Document

### Endpoint

```http
DELETE /api/v1/documents/{document_id}/
```

### Response

```http
204 No Content
```

---

# 7. Analysis History

## Get Analysis

### Endpoint

```http
GET /api/v1/grammar/analyses/{analysis_id}/
```

Returns the analysis and its suggestions.

---

# 8. Download API

## Generate Document Download

### Endpoint

```http
POST /api/v1/documents/{document_id}/download/
```

### Request

```json
{
  "format": "pdf"
}
```

Initial supported format:

```text
pdf
```

Future formats:

```text
docx
txt
```

### Response

The API should return a downloadable file or a secure temporary download URL.

---

# 9. Common Error Response

All APIs should return a consistent error structure.

Example:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The submitted text is invalid.",
    "details": {
      "text": [
        "This field is required."
      ]
    }
  }
}
```

---

# 10. HTTP Status Codes

Use standard HTTP status codes.

| Status | Meaning |
|---|---|
| 200 | Successful request |
| 201 | Resource created |
| 204 | Successful request with no response body |
| 400 | Invalid request |
| 401 | Authentication required/invalid |
| 403 | Permission denied |
| 404 | Resource not found |
| 409 | Conflict |
| 422 | Validation failure where applicable |
| 429 | Rate limit exceeded |
| 500 | Internal server error |
| 503 | External/AI service temporarily unavailable |

---

# 11. Security Rules

- Require authentication for user-specific APIs.
- Verify document ownership before access.
- Never expose another user's documents.
- Validate all incoming data.
- Apply request rate limits where required.
- Do not expose internal exception details.
- Do not expose API keys to the frontend.
- AI provider credentials must remain on the backend.

---

# 12. API Flow

Grammar checking:

```text
React Frontend
      ↓
POST /api/v1/grammar/check/
      ↓
Django REST Framework
      ↓
Validation
      ↓
Grammar / Spelling / AI Services
      ↓
Database
      ↓
JSON Response
      ↓
React UI
```

AI rewrite:

```text
React Frontend
      ↓
POST /api/v1/ai/rewrite/
      ↓
Django REST Framework
      ↓
Validation
      ↓
AI Service
      ↓
Database / History
      ↓
JSON Response
      ↓
React UI
```

---

# 13. API Design Principles

1. Use RESTful resource naming.
2. Keep API responses predictable.
3. Version the API.
4. Use standard HTTP methods.
5. Return meaningful HTTP status codes.
6. Validate input on the backend.
7. Keep authentication separate from business logic.
8. Never trust client-provided user ownership information.
9. Keep AI provider implementation behind backend services.
10. Avoid breaking API changes after frontend integration.

---

# 14. Initial API Scope

For the first working version, implement:

```text
POST   /api/v1/grammar/check/
PATCH  /api/v1/grammar/suggestions/{id}/

POST   /api/v1/ai/rewrite/

GET    /api/v1/documents/
POST   /api/v1/documents/
GET    /api/v1/documents/{id}/
PUT    /api/v1/documents/{id}/
DELETE /api/v1/documents/{id}/

GET    /api/v1/grammar/analyses/{id}/
```

Download can be implemented after the document workflow is stable.

---

# 15. Future APIs

Potential future endpoints:

```text
/api/v1/auth/
/api/v1/dictionary/
/api/v1/translation/
/api/v1/ocr/
/api/v1/voice/
/api/v1/usage/
/api/v1/settings/
```

These should be added only when their requirements are finalized.
