# Veyra — Error Handling

## Error Categories

| Category | Example | Default behavior |
|---|---|---|
| Validation | invalid media | fail immediately |
| Authentication | expired token | pause/fail and request reauthorization |
| Authorization | missing permission | fail and surface action |
| Rate limit | provider quota | backoff/retry |
| Network | timeout | retry when safe |
| Dependency | FUTURE unavailable | retry/backoff |
| Conflict | duplicate idempotency key | return existing result |
| Internal | unexpected exception | record, alert, fail safely |

## Error Envelope
All API errors should normalize to a stable structure with machine-readable code, human-readable message, request ID, and optional safe details.

## Retry Policy
Only explicitly retryable errors are retried. Backoff should prevent thundering-herd behavior. Maximum attempts and terminal state must be persisted.

## Partial Failure
A multi-step workflow must record completed steps. For example, if FUTURE succeeds but Instagram publication fails, Veyra should retain the generated asset and retry publication rather than rerender unnecessarily.

## External Error Preservation
Store provider error codes/messages where safe so operators can diagnose failures. Do not expose raw secrets or internal stack traces to clients.

## Recovery
Every terminal failure should have a clear recovery path: edit content, reconnect account, wait for rate limit, retry, or investigate dependency health.
