# Veyra — MVP

## Goal
Prove the complete reliable loop: **receive → validate → schedule → execute → publish → confirm → record → recover**.

## Included
1. Node.js/TypeScript service foundation.
2. Environment/config validation.
3. Persistent database.
4. Instagram account connection through supported Meta APIs.
5. Content/media representation.
6. Immediate publication.
7. Scheduled publication.
8. Durable job queue and worker.
9. Idempotency and retry handling.
10. Publication history.
11. Structured logging and health checks.
12. API documentation.
13. Automated tests for domain logic and provider boundaries.
14. FUTURE integration contract and a test/mock adapter.

## Explicitly Excluded
- Full social-network abstraction.
- Advanced AI content generation.
- Autonomous growth strategy.
- Complex visual editing.
- Deep analytics dashboards.
- Unofficial browser-based Instagram automation.

## MVP Acceptance Tests
- A connected account can be represented as active.
- A valid publication request creates exactly one logical job.
- A successful job records the provider media ID.
- A transient provider failure retries according to policy.
- A permanent failure reaches a terminal failed state.
- Restarting the worker does not lose pending jobs.
- A scheduled job is not executed before its due time.
- Duplicate requests using the same idempotency key do not create duplicate publications.
- A FUTURE render can be represented as an external job and its returned assets can enter a publication workflow.

## Definition of Done
The MVP is done when a fresh developer can run it from documented instructions and execute the full test workflow without manual database surgery or undocumented steps.
