# Veyra — Data Model

The persistence model should be relational unless implementation evidence proves otherwise.

## Core Entities

### Account
- `id`
- `provider`
- `provider_account_id`
- `display_name`
- `status`
- encrypted credential/token references
- `created_at`, `updated_at`

### Content
- `id`
- `account_id`
- `caption`
- `status`
- `source`
- `version`
- `created_at`, `updated_at`

### Asset
- `id`
- `content_id`
- `storage_url` / provider reference
- `mime_type`
- dimensions
- checksum
- ordering/index
- origin (`upload`, `future`, `generated`, etc.)

### Publication
- `id`
- `content_id`
- `account_id`
- `provider_media_id`
- `status`
- `published_at`
- provider metadata

### Job
- `id`
- `type`
- `status`
- `attempt_count`
- `scheduled_for`
- `started_at`
- `completed_at`
- `last_error`
- `idempotency_key`
- correlation ID

### RenderJob
- `id`
- `content_id`
- external provider (`future`)
- external job ID
- requested design specification
- status
- result/asset references
- error

## Relationships

```text
Account 1 ─── * Content
Content 1 ─── * Asset
Content 1 ─── * Job
Content 1 ─── * Publication
Content 1 ─── * RenderJob
Account 1 ─── * Publication
```

## Data Rules
- Provider IDs are not substitutes for internal stable IDs.
- Idempotency keys are unique within their operation scope.
- Deleting a content record must not erase audit information required to explain an external publication.
- Token material should not be returned from ordinary API responses.
