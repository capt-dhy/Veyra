# Veyra

Veyra is an Instagram automation platform designed to manage the operational side of social publishing while remaining independent from content-generation engines such as FUTURE.

## Mission
Automate the workflow around an Instagram account: content intake, asset coordination, scheduling, publishing, monitoring, and analytics.

## Architectural Boundary
- **Veyra owns:** Instagram integration, account management, content workflows, scheduling, publishing, retries, status tracking, analytics, and operational automation.
- **FUTURE owns:** historical research-to-visual production and rendering. Veyra consumes FUTURE through an API contract and never depends on FUTURE's internal renderer.

## Documentation
See `docs/` for the project charter, PRD, requirements, MVP, architecture, API contracts, FUTURE integration, Instagram integration, security, testing, deployment, monitoring, and roadmap.

## Development Rule
Documentation is the source of truth. Architecture and contracts should be resolved before implementation. Veyra must remain modular so future content engines can be integrated without rewriting the publishing core.
