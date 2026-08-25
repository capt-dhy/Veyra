# Veyra — Automation Engine

## Responsibility
The automation engine executes durable work outside the request/response lifecycle.

## Job Types
- `publish`
- `render-request`
- `render-status`
- future provider synchronization jobs

## Queue Model
A queue-backed worker should claim jobs, execute them, record outcome, and release/complete the job. The database remains the source of durable business state; the queue is an execution mechanism.

## State Machine

```text
QUEUED → RUNNING → SUCCEEDED
              └→ RETRY_WAIT → QUEUED
              └→ FAILED
```

## Scheduling
Store schedules with UTC execution time plus the original timezone context. A scheduler should enqueue due jobs rather than perform the publication itself.

## Retries
Retries require:
- maximum attempt count;
- exponential/backoff strategy;
- classification of retryable errors;
- idempotency protection;
- final failure recording.

## Concurrency
A job must have a lease/claim mechanism so multiple workers cannot execute the same side effect concurrently.

## Dead Letters
Jobs that exhaust retries should become inspectable failed/dead-letter work rather than disappearing.

## Idempotency
Every externally side-effecting workflow should have an idempotency key. The key must be persisted and checked before executing a duplicate operation.

## Recovery
On worker restart, stale running jobs must be detectable and recoverable according to their lease/heartbeat state.

## Future Evolution
The same engine can eventually coordinate content generation, rendering, publishing, analytics synchronization, and other workflows without forcing those capabilities into a single synchronous request.
