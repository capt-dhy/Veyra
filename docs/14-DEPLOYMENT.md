# Veyra — Deployment

## Environments
- **Local:** developer machine with local database/queue and mock integrations.
- **Staging:** production-like environment using test/sandbox credentials where available.
- **Production:** protected environment with real provider credentials and monitored workers.

## Runtime Components
Initial deployment should support:
1. API process.
2. Worker process.
3. Scheduler mechanism or scheduler worker.
4. Relational database.
5. Redis-compatible queue/cache if selected.
6. Object storage if Veyra manages media.

## Configuration
All environment-specific values come from environment/secret management. Validate configuration at startup and fail fast for missing required values.

## Deployment Principle
Build immutable application artifacts, run database migrations explicitly, deploy API and workers with compatible versions, then verify health checks.

## Database Migrations
Migrations must be versioned and repeatable. Production schema changes should be backward-compatible where rolling deployments require old and new application versions to coexist.

## Worker Deployment
Workers should shut down gracefully, stop accepting new jobs, finish/release current work safely, and preserve retryable jobs.

## FUTURE Deployment
FUTURE may run independently. Veyra should know only its base URL, authentication configuration, and API contract version. Local development can use a mock implementation.

## Production Checklist
- secrets configured;
- database reachable;
- queue reachable;
- storage configured;
- Meta/Instagram credentials configured;
- FUTURE endpoint configured if enabled;
- migrations applied;
- health checks passing;
- logs/metrics available;
- backup/recovery strategy verified.
