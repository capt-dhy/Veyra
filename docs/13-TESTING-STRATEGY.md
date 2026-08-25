# Veyra — Testing Strategy

## Testing Pyramid

### Unit
Test domain rules, validation, state transitions, scheduling calculations, retry classification, and idempotency behavior without external services.

### Integration
Test database repositories, queue behavior, storage, and provider adapters against controlled environments/mocks.

### Contract
Test Veyra's FUTURE client against a mock contract server and validate request/response schemas. Test Instagram provider normalization similarly.

### End-to-End
Exercise the complete flow from API request to worker execution and persisted result. Real Instagram tests must be controlled and use authorized test accounts/environments where permitted.

## Critical Scenarios
- successful immediate publication;
- scheduled publication;
- duplicate idempotency request;
- worker restart during a job;
- transient retry;
- permanent provider failure;
- expired authorization;
- FUTURE render success;
- FUTURE render failure;
- FUTURE unavailable;
- completed render followed by failed publication;
- malformed API input.

## Test Rules
- Tests must not depend on production secrets.
- External provider behavior should be deterministic in automated tests.
- State-machine transitions should have explicit tests.
- Every new integration contract needs contract tests.

## Acceptance
A feature is not complete until its happy path, important failure paths, and recovery behavior are tested.
