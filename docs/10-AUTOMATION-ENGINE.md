# Veyra — Automation Engine

## Responsibility

The automation engine executes durable work outside the request/response lifecycle.

## Job Types

- `publish`
- `render-request`
- `render-status`
- `webhook-event`
- `provider-sync`
- future analytics synchronization jobs

## Queue Model

A queue-backed worker should claim jobs, execute them, record outcome, and release/complete the job. The database remains the source of durable business state; the queue is an execution mechanism.

Webhook handlers should enqueue normalized events instead of performing long-running business operations inside the HTTP request.

## Event-Driven Flow

```text
Meta / Instagram
       │
       ▼
Webhook Receiver
       │
       ▼
Validate + Normalize
       │
       ▼
Persist Event
       │
       ▼
Event Queue
       │
       ▼
Worker
       │
       ▼
Automation Action
```

## State Machine

```text
QUEUED → RUNNING → SUCCEEDED
              └→ RETRY_WAIT → QUEUED
              └→ FAILED
```

Inbound events may have their own processing state, for example:

```text
RECEIVED → VALIDATED → QUEUED → PROCESSED
                     └───────→ FAILED
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

Webhook event processing should also distinguish transient processing failures from invalid/permanent events.

## Concurrency

A job must have a lease/claim mechanism so multiple workers cannot execute the same side effect concurrently.

## Dead Letters

Jobs that exhaust retries should become inspectable failed/dead-letter work rather than disappearing.

## Idempotency

Every externally side-effecting workflow should have an idempotency key. The key must be persisted and checked before executing a duplicate operation.

Webhook events should be deduplicated using a provider event identifier or an equivalent deterministic event key when available.

## Recovery

On worker restart, stale running jobs must be detectable and recoverable according to their lease/heartbeat state.

## Webhook Acknowledgement Rule

The webhook HTTP endpoint should acknowledge valid requests quickly. It must not wait for expensive operations such as:

- publishing another post;
- AI processing;
- rendering;
- analytics calculations;
- external service chains.

Those operations belong in the asynchronous execution layer.

## Future Evolution

The same engine can eventually coordinate content generation, rendering, publishing, analytics synchronization, webhook-driven workflows, and other operations without forcing those capabilities into a single synchronous request.
