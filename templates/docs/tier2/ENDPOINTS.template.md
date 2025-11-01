# API Endpoints - {{SERVICE_NAME}}

> 🌐 Documentazione completa degli endpoints API
> Ultimo aggiornamento: {{TIMESTAMP}}

## 📋 Overview

{{SERVICE_NAME}} espone i seguenti endpoints REST API.

**Base URL**: `{{API_BASE_URL}}`
**API Version**: `{{API_VERSION}}`
**Authentication**: {{AUTH_TYPE}}

## 🔐 Autenticazione

{{AUTH_DESCRIPTION}}

**Headers richiesti**:
```http
Authorization: {{AUTH_HEADER_FORMAT}}
Content-Type: application/json
```

**Esempio**:
```bash
curl -H "Authorization: {{AUTH_EXAMPLE}}" \
     -H "Content-Type: application/json" \
     {{API_BASE_URL}}/endpoint
```

## 📚 Endpoints

### Health Check

#### GET `/health`

Check stato dell'API.

**Authentication**: Non richiesta

**Response**: `200 OK`
```json
{
  "status": "healthy",
  "timestamp": "2024-01-01T00:00:00Z",
  "version": "{{API_VERSION}}"
}
```

---

## 👤 User Endpoints

### GET `/users`

Recupera lista utenti.

**Authentication**: Required
**Permissions**: `read:users`

**Query Parameters**:
| Param | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `page` | integer | No | 1 | Page number |
| `limit` | integer | No | 20 | Items per page |
| `sort` | string | No | `created_at` | Sort field |
| `order` | string | No | `desc` | Sort order (asc/desc) |

**Request Example**:
```bash
curl -X GET "{{API_BASE_URL}}/users?page=1&limit=20" \
     -H "Authorization: Bearer YOUR_TOKEN"
```

**Response**: `200 OK`
```json
{
  "data": [
    {
      "id": "uuid",
      "name": "John Doe",
      "email": "john@example.com",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "pages": 5
  }
}
```

**Error Responses**:
- `401 Unauthorized`: Token mancante o invalido
- `403 Forbidden`: Permessi insufficienti
- `500 Internal Server Error`: Errore server

---

### GET `/users/:id`

Recupera dettagli utente specifico.

**Authentication**: Required
**Permissions**: `read:users`

**Path Parameters**:
| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | Yes | User ID |

**Request Example**:
```bash
curl -X GET "{{API_BASE_URL}}/users/123e4567-e89b-12d3-a456-426614174000" \
     -H "Authorization: Bearer YOUR_TOKEN"
```

**Response**: `200 OK`
```json
{
  "id": "123e4567-e89b-12d3-a456-426614174000",
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user",
  "created_at": "2024-01-01T00:00:00Z",
  "updated_at": "2024-01-02T00:00:00Z"
}
```

**Error Responses**:
- `404 Not Found`: User non trovato

---

### POST `/users`

Crea nuovo utente.

**Authentication**: Required
**Permissions**: `create:users`

**Request Body**:
```json
{
  "name": "string (required, 2-100 chars)",
  "email": "string (required, valid email)",
  "password": "string (required, min 8 chars)",
  "role": "string (optional, default: user)"
}
```

**Validation Rules**:
- `name`: Required, 2-100 caratteri
- `email`: Required, formato email valido, unico
- `password`: Required, minimo 8 caratteri, almeno 1 maiuscola, 1 numero
- `role`: Optional, valori: `user`, `admin`

**Request Example**:
```bash
curl -X POST "{{API_BASE_URL}}/users" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "Jane Doe",
       "email": "jane@example.com",
       "password": "SecurePass123"
     }'
```

**Response**: `201 Created`
```json
{
  "id": "uuid",
  "name": "Jane Doe",
  "email": "jane@example.com",
  "role": "user",
  "created_at": "2024-01-01T00:00:00Z"
}
```

**Error Responses**:
- `400 Bad Request`: Validation errors
  ```json
  {
    "error": "Validation failed",
    "details": {
      "email": "Email already exists",
      "password": "Password must be at least 8 characters"
    }
  }
  ```
- `422 Unprocessable Entity`: Invalid data format

---

### PUT `/users/:id`

Aggiorna utente esistente.

**Authentication**: Required
**Permissions**: `update:users` or own user

**Path Parameters**:
| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | Yes | User ID |

**Request Body** (tutti i campi opzionali):
```json
{
  "name": "string",
  "email": "string",
  "role": "string"
}
```

**Request Example**:
```bash
curl -X PUT "{{API_BASE_URL}}/users/123e4567-e89b-12d3-a456-426614174000" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "John Smith"
     }'
```

**Response**: `200 OK`
```json
{
  "id": "uuid",
  "name": "John Smith",
  "email": "john@example.com",
  "updated_at": "2024-01-02T00:00:00Z"
}
```

---

### DELETE `/users/:id`

Elimina utente.

**Authentication**: Required
**Permissions**: `delete:users`

**Path Parameters**:
| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string (UUID) | Yes | User ID |

**Request Example**:
```bash
curl -X DELETE "{{API_BASE_URL}}/users/123e4567-e89b-12d3-a456-426614174000" \
     -H "Authorization: Bearer YOUR_TOKEN"
```

**Response**: `204 No Content`

**Error Responses**:
- `404 Not Found`: User non trovato
- `409 Conflict`: User ha dipendenze che impediscono eliminazione

---

<!-- TODO: Aggiungere altri gruppi di endpoints -->

## 🔧 Rate Limiting

{{RATE_LIMIT_DESCRIPTION}}

**Limits**:
- **Anonymous**: {{ANON_RATE_LIMIT}} requests/minute
- **Authenticated**: {{AUTH_RATE_LIMIT}} requests/minute
- **Premium**: {{PREMIUM_RATE_LIMIT}} requests/minute

**Headers**:
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
```

## 📊 Response Formats

### Success Response Structure

```json
{
  "data": {}, // or []
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "version": "{{API_VERSION}}"
  }
}
```

### Error Response Structure

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable message",
    "details": {},
    "timestamp": "2024-01-01T00:00:00Z"
  }
}
```

### Pagination Structure

```json
{
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "pages": 5,
    "hasNext": true,
    "hasPrev": false
  }
}
```

## ❌ Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `AUTH_REQUIRED` | 401 | Authentication required |
| `INVALID_TOKEN` | 401 | Invalid or expired token |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 400 | Request validation failed |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Internal server error |

## 🧪 Testing

### Postman Collection

[Download Postman Collection]({{POSTMAN_COLLECTION_URL}})

### OpenAPI/Swagger

API documentation is available at:
- **Swagger UI**: {{SWAGGER_URL}}
- **OpenAPI Spec**: {{OPENAPI_SPEC_URL}}

### Example Scripts

```bash
# Health check
./scripts/test-health.sh

# Full API test suite
./scripts/test-api.sh
```

## 📝 Changelog

### {{VERSION}} - {{DATE}}
- Added: New endpoint `/users`
- Changed: Updated authentication flow
- Deprecated: Old `/auth` endpoint (use `/auth/v2`)
- Removed: Legacy `/v1` prefix
- Fixed: Pagination bug in list endpoints

<!-- TODO: Aggiungere altre versioni -->

## 🔗 Related Documentation

- [Authentication Guide](../operations/security.md)
- [Database Schema](./database.md)
- [Error Handling](./troubleshooting.md)

---
*Generato automaticamente da Toduba System v{{TODUBA_VERSION}}*
