# Veyra — Requirements

## Functional Requirements

### Accounts
- Connect and disconnect supported Instagram accounts through official Meta authentication flows.
- Store provider IDs and token metadata securely.
- Detect expired/revoked authorization and surface actionable status.

### Content
- Create, update, validate, schedule, cancel, and archive content items.
- Associate one or more media assets with a publication.
- Preserve content source and version metadata.

### Publishing
- Publish supported Instagram media through the provider adapter.
- Track provider request IDs and returned media IDs.
- Prevent accidental duplicate publication through idempotency keys.

### Scheduling
- Accept an explicit timezone-aware publication time.
- Persist scheduled jobs durably.
- Execute jobs at or after their due time.
- Ensure a scheduled job is not executed concurrently by multiple workers.

### Jobs
- Persist state transitions.
- Record attempts and errors.
- Retry only errors classified as transient.
- Support cancellation where the job has not passed an irreversible stage.

### FUTURE integration
- Submit a rendering request containing content and design specifications.
- Receive a durable FUTURE job ID.
- Track render status.
- Retrieve or receive generated asset references.
- Validate assets before publishing.
- Never require Veyra to know FUTURE's renderer internals.

## Non-Functional Requirements

### Reliability
Publishing state must survive process restarts. A worker crash must not silently lose a job.

### Security
Secrets must be supplied through environment/secret management and never committed. Sensitive tokens must be encrypted at rest where persisted.

### Performance
API requests should remain responsive by moving long-running rendering and publishing work to background jobs.

### Observability
Every job must have a correlation ID, structured logs, status timestamps, and an actionable failure reason.

### Maintainability
Provider integrations, domain logic, queues, persistence, and HTTP transport must have clear boundaries.

### Compliance
The implementation must use supported Instagram/Meta APIs and respect applicable platform policies and permissions.

## Requirement Priority
P0 = required for MVP. P1 = important after MVP. P2 = future.

| Area | Requirement | Priority |
|---|---|---|
| Accounts | Official account connection | P0 |
| Content | Draft and validate content | P0 |
| Publishing | Image/carousel publishing supported by current API capabilities | P0 |
| Scheduling | Durable scheduled jobs | P0 |
| Jobs | Retry/idempotency/state tracking | P0 |
| FUTURE | Render request/asset handoff contract | P0 architecture, P1 operational integration |
| Analytics | Basic publication metrics | P1 |
| Multi-platform | Other social providers | P2 |
| Autonomous optimization | AI-driven strategy | P2 |
