# Veyra — Instagram Integration

## 1. Provider Strategy

Instagram/Meta communication must be isolated behind an adapter. Domain code should ask the provider to publish a publication; it should not know raw HTTP details.

Veyra must implement against the **current Meta/Instagram APIs and documentation available at implementation time**. API versions, permissions, account eligibility, supported media formats, rate limits, review requirements, and webhook capabilities must be verified before implementation.

## 2. Account Model

The initial product targets Instagram **professional accounts** supported by Meta's current API model. Veyra must not assume that personal accounts expose the same capabilities.

The exact authorization flow and required permissions must be validated against current Meta documentation before production implementation.

## 3. Publishing API vs Webhooks

These are separate capabilities and must not be conflated.

### Publishing API

The publishing API is used when Veyra needs to **send an action to Instagram**, such as creating/publishing supported media.

Conceptually:

```text
VEYRA
  │
  │ publish request
  ▼
Meta / Instagram API
  │
  ▼
Instagram
```

Veyra's initial MVP should focus on establishing a reliable publishing path. Webhooks are **not a prerequisite for the basic publish request itself**.

### Webhooks

Webhooks are used when Meta needs to **notify Veyra about supported events** rather than Veyra repeatedly polling for changes.

Conceptually:

```text
Instagram / Meta
       │
       │ supported event
       ▼
Veyra Webhook Endpoint
       │
       ▼
Event Validation
       │
       ▼
Internal Event / Queue
       │
       ▼
Automation Engine
```

Webhook support should be introduced when Veyra implements event-driven features that benefit from it, such as supported comments, messaging/events, or publication/account events. The exact event subscriptions must be verified against the current Meta documentation because supported webhook fields and requirements can change.

## 4. Webhook Lifecycle

When webhooks are enabled, the receiver should follow this lifecycle:

1. Receive Meta's webhook verification request when applicable.
2. Validate the verification request according to Meta's current requirements.
3. Accept only requests that pass the configured verification/security checks.
4. Parse and validate the event payload.
5. Record an event identifier or deduplication key where available.
6. Reject duplicate events safely.
7. Persist the normalized internal event.
8. Acknowledge the webhook quickly.
9. Process business logic asynchronously through the automation/event system.
10. Record processing success or failure.

The HTTP webhook handler should **not** perform long-running work synchronously.

## 5. Webhook Security

Webhook endpoints are public-facing infrastructure and must be treated as untrusted input.

Requirements:

- Keep webhook verification secrets/configuration outside source control.
- Validate Meta's documented authenticity/signature mechanism where applicable.
- Validate payload structure and expected event types.
- Apply request-size limits.
- Protect against replay/duplicate processing where the platform provides identifiers or timestamps.
- Never trust account IDs, media IDs, or event data without validating their relationship to a connected Veyra account.
- Log enough metadata for debugging without logging secrets or sensitive tokens.

## 6. Integration Responsibilities

- OAuth/account authorization.
- Secure token lifecycle.
- Provider account identification.
- Media upload/container creation where required.
- Publication/publish calls.
- Provider response normalization.
- Webhook verification and event ingestion where enabled.
- Rate-limit and transient error handling.
- Provider-specific capability checks.

## 7. Provider Interface Concept

```ts
interface InstagramProvider {
  connect(input: ConnectInput): Promise<AccountConnection>;
  validatePublication(input: PublicationInput): Promise<ValidationResult>;
  publish(input: PublicationInput): Promise<ProviderPublicationResult>;
  getPublication(id: string): Promise<ProviderPublication>;
}
```

Webhook handling should be represented as a separate inbound adapter rather than added as a side effect to the publishing interface.

```ts
interface InstagramWebhookAdapter {
  verify(input: WebhookVerificationInput): WebhookVerificationResult;
  parse(input: WebhookRequest): Promise<NormalizedInstagramEvent[]>;
}
```

These interfaces are conceptual; exact implementation belongs in the architecture/codebase.

## 8. Publishing Safety

Before an external side effect:

- validate account state;
- validate media;
- validate caption/metadata;
- verify the job is still executable;
- enforce idempotency;
- record an attempt.

## 9. Platform Constraints

Do not assume that every Instagram feature can be automated. Feature availability can vary by account type, API version, permission, media format, product configuration, and Meta policy.

The implementation must verify current official Meta documentation before enabling a capability. Documentation in this repository should record the API version, permissions, and assumptions used by the implementation.

## 10. Failure Handling

- Authentication/permission failure → stop and surface reauthorization.
- Rate limit → retry with backoff according to provider guidance.
- Network timeout → retry if idempotency/operation safety permits.
- Invalid media/request → permanent failure requiring correction.
- Invalid webhook payload → reject or quarantine safely without executing business logic.
- Duplicate webhook event → acknowledge safely without repeating side effects.
- Unknown provider error → preserve provider context and fail safely until classified.

## 11. MVP Integration Sequence

```text
Phase 1
Account authorization
       ↓
Media validation
       ↓
Instagram publishing
       ↓
Publication state tracking

Phase 2
Webhook receiver
       ↓
Event normalization
       ↓
Internal event queue
       ↓
Event-driven automation
```

This prevents the MVP from becoming dependent on webhook infrastructure before there is a feature that requires it.
