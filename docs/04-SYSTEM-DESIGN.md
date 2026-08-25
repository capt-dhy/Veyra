# Veyra — System Design

## Logical Flow

```text
Client/API
   ↓
HTTP/Application Layer
   ↓
Domain Services
   ├── Content Service
   ├── Scheduling Service
   ├── Publishing Service
   └── Integration Service
           ├── Instagram Provider
           └── FUTURE Client
   ↓
Job Queue
   ↓
Workers
   ↓
Provider APIs / FUTURE
   ↓
Persistence + Events + Logs
```

## Publication Lifecycle

```text
DRAFT
  ↓ validate
READY
  ↓ schedule
SCHEDULED
  ↓ due
QUEUED
  ↓ worker
PUBLISHING
  ├── success → PUBLISHED
  └── transient failure → RETRY_WAIT → QUEUED
                         permanent failure → FAILED
```

## Render Lifecycle

```text
REQUESTED → RENDERING → READY
                    ↘ FAILED
```

Veyra should treat FUTURE as an external dependency. Render state is mapped into Veyra's workflow without importing FUTURE internals.

## Key Design Decisions
- HTTP handlers do not directly perform long-running publishing.
- Workers own asynchronous execution.
- Domain services do not know HTTP details.
- Provider adapters isolate external APIs.
- Persistence records state needed to recover after crashes.
- Idempotency is required at boundaries that can create external side effects.
- All timestamps are stored in UTC; user-facing schedules retain timezone information.

## Failure Model
Failures are classified as validation, authentication, authorization, rate-limit, provider, network, dependency, or internal errors. The classification determines whether the system retries, pauses, or fails permanently.

## Eventual Consistency
Publication and rendering are asynchronous. The API should return job/resource state rather than pretend an external operation completed synchronously.
