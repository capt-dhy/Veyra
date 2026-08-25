# Veyra — Product Requirements Document

## Product
Veyra is an Instagram operations and automation platform. It manages the lifecycle of content from intake through scheduling, publishing, status tracking, and operational analytics.

## Users
- Solo creators and operators managing one or more Instagram accounts.
- Small teams that need repeatable publishing workflows.
- Internal applications that need a publishing API.

## Core User Journey
1. Connect an eligible Instagram account.
2. Create or receive a content item.
3. Attach media and metadata.
4. Validate the publication.
5. Publish immediately or schedule it.
6. Track the job until Instagram confirms the result.
7. Record the publication and expose its status/metrics.

## Core Capabilities
### Account management
Store a safe representation of connected accounts, provider identifiers, token metadata, permissions, and connection status.

### Content management
Draft, validate, version, and queue content. A content item can contain caption, media references, alt text, hashtags, publishing options, and source metadata.

### Scheduling
Accept future publication times, normalize time zones, prevent duplicate execution, and persist schedule state.

### Publishing
Use the supported Meta/Instagram APIs through an isolated provider adapter. Veyra must not rely on browser automation for normal publishing where official APIs are available.

### Job orchestration
Represent asynchronous operations as durable jobs with states, attempts, timestamps, errors, and correlation IDs.

### Asset coordination
Accept externally generated assets. Veyra may request visual assets from FUTURE through an API contract, but Veyra does not render them internally.

### Observability
Expose health, job status, structured logs, and operational metrics.

## FUTURE Workflow
A content workflow may request a design from FUTURE by sending structured content plus a design/template specification. Veyra receives a job ID, waits/polls or receives a callback, validates the returned assets, then uses them in an Instagram publication job.

## MVP Boundaries
MVP prioritizes one Instagram provider, one account workflow, image/carousel publishing where officially supported, immediate and scheduled publishing, durable job tracking, retries, and a clean API. Advanced analytics, multi-platform support, autonomous optimization, and visual editing are later phases.

## UX Principle
The operator should understand what Veyra is doing at every point: draft, scheduled, rendering, ready, publishing, published, retrying, or failed.

## Acceptance Principle
Every product feature must have observable success criteria and a failure path before implementation is considered complete.
