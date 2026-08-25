# Veyra — API Specification

This document defines the conceptual API contract. Exact framework syntax and generated OpenAPI details belong to implementation.

## API Principles

- Version public APIs (`/api/v1`).
- JSON request/response bodies.
- Explicit resource states.
- Idempotency for side-effecting operations.
- Consistent error envelope.
- Never expose secrets.
- Keep provider-specific APIs behind adapters.
- Treat inbound webhooks as untrusted external events.

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

## Instagram Webhook Endpoint

Webhook routes are provider-facing endpoints rather than normal client resources.

Conceptually:

`GET /webhooks/instagram`

Used for the provider's webhook verification handshake when required by the current Meta integration.

`POST /webhooks/instagram`

Receives supported Instagram/Meta event notifications.

The webhook handler should:

1. Verify/authenticate the request according to Meta's current requirements.
2. Validate the payload.
3. Normalize supported events into Veyra's internal event model.
4. Deduplicate where necessary.
5. Persist/enqueue the event.
6. Return an acknowledgement quickly.

Long-running automation must happen asynchronously.

The exact verification parameters, signature mechanism, subscribed fields, and response requirements must be taken from the current Meta documentation during implementation.

## Internal Event Contract

Normalized provider events should resemble:

```json
{
  "id": "evt_123",
  "provider": "instagram",
  "type": "provider.event.type",
  "accountId": "acc_123",
  "occurredAt": "2026-09-01T18:30:00Z",
  "data": {},
  "sourceEventId": "meta-event-123"
}
```

`type` and `data` are intentionally provider-normalized so business logic does not depend on raw Meta payload structures.

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

Webhook authentication is handled separately through provider verification/signature mechanisms and must never be treated as ordinary user authentication.
