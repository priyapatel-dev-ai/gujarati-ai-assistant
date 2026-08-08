# Grammar Checker - Database Design

## 1. Purpose

Define the database structure required for the Gujarati AI Writing Assistant Grammar Checker.

The design should support users, documents, grammar analysis, suggestions, corrections, and AI rewrite history.

---

## 2. Database

Primary database:

- PostgreSQL

The database should use relational tables with clear primary-key and foreign-key relationships.

---

## 3. Core Tables

### 3.1 User

The application user.

Suggested fields:

| Field | Type | Notes |
|---|---|---|
| id | UUID / BigInt | Primary Key |
| email | String | Unique |
| password_hash | String | Stored securely |
| first_name | String | Optional |
| last_name | String | Optional |
| is_active | Boolean | Account status |
| created_at | DateTime | Creation time |
| updated_at | DateTime | Last update |

Authentication implementation will be finalized separately.

---

### 3.2 Document

Stores user-created writing documents.

| Field | Type | Notes |
|---|---|---|
| id | UUID / BigInt | Primary Key |
| user_id | FK | Owner |
| title | String | Document title |
| content | Text | Gujarati content |
| language | String | Default: gu |
| created_at | DateTime | Creation time |
| updated_at | DateTime | Last update |

Relationship:

```text
User 1 ──────── * Document
```

One user can have many documents.

---

### 3.3 GrammarAnalysis

Stores an analysis session for a document.

| Field | Type | Notes |
|---|---|---|
| id | UUID / BigInt | Primary Key |
| document_id | FK | Related document |
| status | String | pending / completed / failed |
| analysis_version | String | Engine/version |
| created_at | DateTime | Start time |
| completed_at | DateTime | Completion time |
| error_message | Text | Optional |

Relationship:

```text
Document 1 ──────── * GrammarAnalysis
```

A document can have multiple analysis sessions.

---

### 3.4 Suggestion

Stores individual grammar, spelling, or improvement suggestions.

| Field | Type | Notes |
|---|---|---|
| id | UUID / BigInt | Primary Key |
| analysis_id | FK | Related analysis |
| type | String | grammar / spelling / improvement / ai |
| original_text | Text | Original text |
| suggested_text | Text | Suggested correction |
| explanation | Text | User-facing explanation |
| start_position | Integer | Text position |
| end_position | Integer | Text position |
| status | String | pending / accepted / rejected / ignored |
| created_at | DateTime | Creation time |

Relationship:

```text
GrammarAnalysis 1 ──────── * Suggestion
```

---

### 3.5 SavedCorrection

Stores user-applied corrections when correction history is required.

| Field | Type | Notes |
|---|---|---|
| id | UUID / BigInt | Primary Key |
| document_id | FK | Related document |
| suggestion_id | FK | Related suggestion |
| original_text | Text | Before correction |
| corrected_text | Text | After correction |
| created_at | DateTime | Applied time |

Relationship:

```text
Document 1 ──────── * SavedCorrection
Suggestion 1 ──────── * SavedCorrection
```

This table may be simplified or removed if correction history is not required in the first release.

---

### 3.6 AIRewrite

Stores AI rewrite requests and results.

| Field | Type | Notes |
|---|---|---|
| id | UUID / BigInt | Primary Key |
| document_id | FK | Related document |
| user_id | FK | Requesting user |
| source_text | Text | Selected/original text |
| rewrite_type | String | simple / professional / formal / shorten / expand |
| result_text | Text | AI-generated result |
| status | String | pending / completed / failed |
| created_at | DateTime | Request time |

Relationship:

```text
User 1 ──────── * AIRewrite
Document 1 ──────── * AIRewrite
```

---

## 4. Entity Relationship Overview

```text
User
 │
 ├───────────────< Document
 │                    │
 │                    ├───────────< GrammarAnalysis
 │                    │                  │
 │                    │                  └───────────< Suggestion
 │                    │
 │                    ├───────────< SavedCorrection
 │                    │
 │                    └───────────< AIRewrite
 │
 └───────────────< AIRewrite
```

---

## 5. Important Design Rules

### User Ownership

A user must only be able to access their own documents and related analysis data.

### Document History

Documents should support `created_at` and `updated_at`.

### Analysis History

A new grammar analysis can create a new `GrammarAnalysis` record rather than overwriting previous analysis data.

### Suggestion Status

Suggestion lifecycle:

```text
pending
   ↓
accepted
   OR
rejected
   OR
ignored
```

### Security

Never store plaintext passwords.

Authentication and authorization will be documented separately.

### Gujarati Support

All text fields containing Gujarati content must support Unicode correctly.

PostgreSQL should use its standard Unicode-capable configuration.

---

## 6. First Release Scope

For the first working version, prioritize:

- User
- Document
- GrammarAnalysis
- Suggestion

AIRewrite can be implemented with the first AI feature.

SavedCorrection can remain optional until correction history is finalized.

---

## 7. Future Tables

Potential future tables:

- UserSettings
- Subscription
- Payment
- Usage
- DictionaryEntry
- CustomDictionaryEntry
- Translation
- OCRDocument
- VoiceTranscription
- AuditLog
- Notification

These should not be implemented until their requirements are finalized.

---

## 8. Database Design Principle

Do not create tables only because a future feature might exist.

Start with the minimum required schema, keep relationships clear, and add tables when the corresponding feature requirements are confirmed.
