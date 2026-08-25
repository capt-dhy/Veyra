# Veyra — Architecture

## Architectural Style
Veyra should begin as a modular backend rather than premature microservices. Internal modules communicate through application interfaces; external systems are accessed through adapters. The design must leave room to extract high-load workers later.

## Modules
- `api`: HTTP routes, serialization, authentication middleware.
- `application`: use cases and orchestration.
- `domain`: content, publication, scheduling, and job rules.
- `integrations`: Instagram/Meta and FUTURE clients.
- `workers`: queue consumers and scheduled execution.
- `persistence`: repositories and database mappings.
- `observability`: logs, metrics, tracing, health.
- `config`: validated environment configuration.

## Boundary Rule
Business logic must not call raw Meta SDK/API code directly. It must depend on an Instagram provider interface.

The same rule applies to FUTURE: Veyra depends on a FutureRenderer interface, not FUTURE's source code.

## Infrastructure
Expected infrastructure includes a relational database, Redis-compatible queue/cache where needed, object storage for managed assets, a worker process, and an HTTP API process. Exact providers are a deployment decision.

## Scaling Path
Start with one application and worker. As workload increases, scale workers independently. Extract services only when a clear operational boundary or load profile justifies it.

## Security Boundary
Secrets, provider tokens, internal service credentials, and user-controlled content are untrusted/sensitive inputs and must be handled at explicit boundaries.
