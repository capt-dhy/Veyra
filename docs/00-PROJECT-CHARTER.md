# Veyra — Project Charter

**Status:** Planning  
**Repository:** `capt-dhy/Veyra`  
**Version:** 0.1.0  
**Document Type:** Project Charter  

---

## 1. Project Overview

**Veyra** is an Instagram automation platform designed to automate the operational workflow surrounding an Instagram account: content intake, asset coordination, scheduling, publishing, execution tracking, retries, and performance monitoring.

Veyra is intentionally designed as an independent system. It is not a historical content generator and it does not own the visual rendering pipeline used by the separate **FUTURE** project.

Veyra's role is to act as the operational layer between content/assets and Instagram.

The long-term vision is to make Veyra capable of receiving content from multiple sources, coordinating generated assets, executing reliable publishing workflows, and exposing reusable automation capabilities through its own API.

---

## 2. Problem Statement

Managing an Instagram account at scale requires repeated manual work across several disconnected activities:

- Preparing or receiving content.
- Preparing media assets in Instagram-compatible formats.
- Writing or attaching captions and metadata.
- Scheduling publications.
- Publishing at the intended time.
- Handling failures and retries.
- Tracking publication status.
- Monitoring account and post performance.
- Maintaining a reliable history of what was published and when.

Existing automation tools often focus narrowly on scheduling or publishing. Veyra aims to treat Instagram operations as a complete software workflow rather than a single posting endpoint.

---

## 3. Vision

> **Build a reliable software engine that can operate an Instagram content workflow with minimal manual intervention while remaining modular enough to integrate with independently built content-generation systems.**

Veyra should eventually behave less like a simple Instagram bot and more like an **automation operating layer** for an Instagram account.

---

## 4. Core Objectives

### Primary objectives

1. Build a reliable Instagram publishing core.
2. Provide a structured content workflow from draft to published state.
3. Support scheduled publishing and automated execution.
4. Track jobs, posts, failures, retries, and publication history.
5. Create a clean API that other applications can use to submit publishing jobs.
6. Integrate with external/internal content engines without coupling Veyra to their implementation details.
7. Establish a strong foundation for future analytics and optimization capabilities.

### Secondary objectives

1. Make the system observable and debuggable.
2. Keep provider-specific Instagram logic isolated from business logic.
3. Design for multiple content sources and future social platforms without prematurely building a multi-platform monolith.
4. Make local development and production deployment reproducible.

---

## 5. Non-Goals

The following are explicitly outside the initial Veyra scope:

- Building a historical research engine.
- Building a general-purpose image editor.
- Reimplementing FUTURE's rendering engine inside Veyra.
- Building a replacement for Instagram itself.
- Automatically scraping Instagram in ways that violate Meta policies or platform restrictions.
- Building every social platform integration in V1.
- Building advanced autonomous marketing intelligence before the publishing foundation is reliable.

---

## 6. Veyra and FUTURE Boundary

Veyra and FUTURE are separate projects with separate responsibilities.

### FUTURE owns

- Historical research representation.
- Historical evidence and verification.
- Editorial transformation for historical stories.
- Design specifications and themes for its supported visual formats.
- Programmatic visual generation.
- Browser-based rendering.
- Exporting final visual assets.

### Veyra owns

- Content workflow management.
- Instagram account and connection management.
- Publishing jobs.
- Scheduling.
- Publication state tracking.
- Retry and failure handling.
- Asset intake and coordination.
- Instagram API communication.
- Publishing history.
- Operational analytics.

### Integration principle

Veyra must communicate with FUTURE through a defined service contract rather than depending on FUTURE's internal source code, renderer, templates, or filesystem.

Conceptually:

```text
┌─────────────────────┐
│       VEYRA         │
│                     │
│ Content Workflow    │
│ Scheduling          │
│ Publishing          │
│ Job Management      │
└──────────┬──────────┘
           │
           │ API contract
           │ content + design request
           ▼
┌─────────────────────┐
│       FUTURE        │
│                     │
│ Research/Editorial  │
│ Design Engine       │
│ Rendering           │
└──────────┬──────────┘
           │
           │ generated assets
           ▼
┌─────────────────────┐
│       VEYRA         │
│                     │
│ Asset intake        │
│ Publishing          │
└──────────┬──────────┘
           │
           ▼
       Instagram
```

This boundary is a foundational architectural constraint, not an implementation detail.

---

## 7. Core Product Principles

### 7.1 Automation over repetition

If a task must be repeated reliably, Veyra should represent it as a workflow rather than rely on manual intervention.

### 7.2 APIs over hidden coupling

Every major subsystem should communicate through explicit contracts. Internal implementation details must not leak across service boundaries.

### 7.3 Reliability before intelligence

A system that generates brilliant content but misses scheduled publications is not a reliable automation platform. Publishing correctness comes before advanced optimization.

### 7.4 Observable automation

Every automated operation should have a traceable state, outcome, and failure reason.

### 7.5 Provider isolation

Instagram/Meta-specific API behavior should live behind a provider boundary so the rest of Veyra is not tightly coupled to raw platform calls.

### 7.6 Content/design separation

Veyra should treat content and visual assets as structured inputs. It should not need to understand how an asset was designed.

### 7.7 Build reusable infrastructure

Veyra should expose reusable primitives that can eventually serve other applications, while keeping the initial product focused on Instagram.

---

## 8. Initial System Lifecycle

The intended high-level workflow is:

```text
Content Source
     │
     ▼
Content Intake
     │
     ▼
Content Validation
     │
     ▼
Asset Coordination ───────► FUTURE / Other Content Engine
     │                              │
     │                              ▼
     │                         Generated Assets
     │                              │
     └───────────────◄──────────────┘
     │
     ▼
Publication Draft
     │
     ▼
Schedule / Immediate Publish
     │
     ▼
Publishing Job
     │
     ▼
Instagram Provider
     │
     ▼
Published / Failed
     │
     ▼
History + Metrics
```

This is a conceptual lifecycle. Detailed behavior belongs in the PRD, requirements, system design, and API specification.

---

## 9. Initial MVP Definition

The first production-oriented milestone should prove that Veyra can reliably:

1. Connect an authorized Instagram account through supported Meta APIs.
2. Accept a publication request containing media and publishing metadata.
3. Validate the request before execution.
4. Create and execute an Instagram publishing job.
5. Track the job through explicit states.
6. Record successful publication information.
7. Record failures with actionable error information.
8. Retry eligible transient failures safely.
9. Schedule a publication for a future time.
10. Expose API endpoints for these operations.

Advanced analytics, autonomous optimization, multi-platform publishing, and sophisticated content intelligence should follow only after the core publishing loop is reliable.

---

## 10. Success Criteria

The project should be considered successful when a developer can:

- Run Veyra locally from documented instructions.
- Configure the required environment variables safely.
- Connect an authorized Instagram account.
- Submit a valid media publication job through the Veyra API.
- Schedule or execute the job.
- Observe the job state transition.
- Confirm the publication result.
- Inspect logs and persisted job history.
- Simulate a failure and verify retry/error behavior.
- Integrate a content-generation service such as FUTURE without importing its internal code.

---

## 11. Technical Direction

The exact stack will be finalized in the architecture documentation, but Veyra is expected to favor a TypeScript/Node.js backend because the project requires:

- API development.
- Asynchronous jobs.
- Scheduling.
- External API integrations.
- Queue-based execution.
- Strong runtime validation.
- Maintainable service boundaries.

Technology choices must serve the architecture rather than dictate it.

---

## 12. Security and Compliance Principles

Veyra will handle account credentials, access tokens, media, and publishing operations. Security is therefore a first-class requirement.

The system must:

- Never commit secrets or access tokens to source control.
- Encrypt sensitive credentials where persistent storage is required.
- Minimize token exposure between services.
- Validate and sanitize externally supplied data.
- Implement authorization around account-level operations.
- Respect Meta/Instagram platform policies and API restrictions.
- Maintain auditable publishing activity.

Detailed security requirements belong in `docs/11-SECURITY.md`.

---

## 13. Documentation Strategy

The `docs/` directory is the engineering source of truth for Veyra.

Planned documentation:

| Document | Purpose |
|---|---|
| `00-PROJECT-CHARTER.md` | Vision, boundaries, principles, objectives |
| `01-PRD.md` | Product behavior and user-facing requirements |
| `02-REQUIREMENTS.md` | Functional and non-functional requirements |
| `03-MVP.md` | Explicit MVP scope and acceptance criteria |
| `04-SYSTEM-DESIGN.md` | End-to-end system behavior |
| `05-ARCHITECTURE.md` | Services, modules, infrastructure, boundaries |
| `06-DATA-MODEL.md` | Persistence model and relationships |
| `07-API-SPECIFICATION.md` | Public/internal API contracts |
| `08-FUTURE-INTEGRATION.md` | Veyra ↔ FUTURE integration contract |
| `09-INSTAGRAM-INTEGRATION.md` | Meta/Instagram integration design |
| `10-AUTOMATION-ENGINE.md` | Jobs, queues, scheduling, workers, retries |
| `11-SECURITY.md` | Security model and secret management |
| `12-ERROR-HANDLING.md` | Failure taxonomy and recovery strategy |
| `13-TESTING-STRATEGY.md` | Unit, integration, contract, and end-to-end testing |
| `14-DEPLOYMENT.md` | Local, staging, and production deployment |
| `15-MONITORING.md` | Logs, metrics, health checks, and observability |
| `16-ROADMAP.md` | Implementation phases and future capabilities |

Documentation should be written before implementation where the decision affects architecture or cross-service contracts.

---

## 14. Development Philosophy

Veyra will be built incrementally.

Each phase should produce a working, testable capability rather than a large amount of disconnected infrastructure.

The preferred sequence is:

```text
Plan
  ↓
Define contracts
  ↓
Design architecture
  ↓
Build smallest reliable vertical slice
  ↓
Test
  ↓
Observe
  ↓
Refine
  ↓
Expand
```

No subsystem should be introduced merely because it might be useful someday. Future extensibility should come from clean boundaries and contracts, not speculative complexity.

---

## 15. Project Completion Definition

Veyra is not complete merely because it can publish an Instagram post.

The project reaches its initial production milestone when the entire operational loop is reliable:

> **Receive → validate → schedule → execute → publish → confirm → record → recover from failure → observe.**

The larger vision is to turn that loop into reusable automation infrastructure while preserving the independence of content-generation systems such as FUTURE.

---

## 16. Next Documents

After this charter, the project should define:

1. Product requirements in `01-PRD.md`.
2. Functional/non-functional requirements in `02-REQUIREMENTS.md`.
3. Explicit MVP scope in `03-MVP.md`.
4. System behavior and architecture in subsequent documents.

**Implementation should not begin until the relevant architectural decisions and service contracts have been documented and reviewed.**
