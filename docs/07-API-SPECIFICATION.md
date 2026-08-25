# Veyra — API Specification

This document defines the conceptual API contract. Exact framework syntax and generated OpenAPI details belong to implementation.

## API Principles
- Version public APIs (`/api/v1`).
- JSON request/response bodies.
- Explicit resource states.
- Idempotency for side-effecting operations.
- Consistent error envelope.
- Never expose secrets.

## Resources

### Accounts
`GET /api/v1/accounts`
`POST /api/v1/accounts`
`GET /api/v1/accounts/:id`
`DELETE /api/v1/accounts/:id`

### Content
`POST /api/v1/content`
`GET /api/v1/content/:id`
`PATCH /api/v1/content/:id`
`POST /api/v1/content/:id/validate`

### Publications
`POST /api/v1/publications`
`GET /api/v1/publications/:id`
`POST /api/v1/publications/:id/cancel`

Example request:

```json
{
  "accountId": "acc_123",
  "contentId": "content_123",
  "publishAt": "2026-09-01T18:30:00Z",
  "idempotencyKey": "publish-content-123-v1"
}
```

Example response:

```json
{
  "id": "job_123",
  "status": "scheduled",
  "scheduledFor": "2026-09-01T18:30:00Z"
}
```

### Jobs
`GET /api/v1/jobs/:id`

## FUTURE Endpoints Used by Veyra
The exact FUTURE API is negotiated separately, but the conceptual contract is:

`POST /render-jobs`

Request contains content data, assets, design/template reference, theme, and output requirements.

Response:

```json
{
  "id": "future_job_123",
  "status": "queued"
}
```

`GET /render-jobs/:id`

Completed response returns generated asset references and metadata.

## Error Envelope

```json
{
  "error": {
    "code": "PUBLICATION_NOT_READY",
    "message": "Required media assets are not ready.",
    "requestId": "req_123"
  }
}
```

## Authentication
Authentication mechanism will be selected during implementation. Every account mutation and publication operation must be authorized against the caller's scope.
