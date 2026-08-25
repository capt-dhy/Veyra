# Veyra — Roadmap

## Phase 0 — Planning
- Project charter.
- PRD and requirements.
- Architecture and data model.
- API and integration contracts.
- Security, testing, deployment, and monitoring plans.

## Phase 1 — Publishing Foundation
- Project scaffolding.
- Configuration.
- Database.
- Account representation.
- Instagram provider adapter.
- Basic publication flow.

## Phase 2 — Automation
- Queue/worker system.
- Scheduling.
- Idempotency.
- Retries.
- Publication history.
- Operational observability.

## Phase 3 — FUTURE Integration
- FutureRenderer interface.
- Mock FUTURE adapter.
- Render-job persistence.
- Real FUTURE API integration.
- Asset validation and handoff to publishing.

## Phase 4 — Content Operations
- Rich content management.
- Reusable workflows.
- Better media lifecycle.
- Operator interface if required.

## Phase 5 — Analytics
- Publication metrics.
- Performance history.
- Reporting.
- Eventually, experiment/optimization primitives.

## Phase 6 — Platform Expansion
Only after the Instagram core is stable:
- additional providers;
- reusable automation APIs;
- multi-account workflows;
- broader social automation.

## Guiding Rule
Do not build the final giant platform first. Build reliable independent capabilities, prove their boundaries, then compose them.
