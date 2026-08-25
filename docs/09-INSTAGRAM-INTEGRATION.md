# Veyra — Instagram Integration

## Provider Strategy
Instagram/Meta communication must be isolated behind an adapter. Domain code should ask the provider to publish a publication; it should not know raw HTTP details.

## Official APIs
The implementation must use the current supported Meta/Instagram APIs and permissions for the chosen account type. API capabilities, publishing formats, rate limits, authentication flows, and eligibility must be verified against current Meta documentation during implementation.

## Integration Responsibilities
- OAuth/account authorization.
- Secure token lifecycle.
- Provider account identification.
- Media upload/container creation where required.
- Publication/publish calls.
- Provider response normalization.
- Rate-limit and transient error handling.

## Provider Interface Concept

```ts
interface InstagramProvider {
  connect(input: ConnectInput): Promise<AccountConnection>;
  validatePublication(input: PublicationInput): Promise<ValidationResult>;
  publish(input: PublicationInput): Promise<ProviderPublicationResult>;
  getPublication(id: string): Promise<ProviderPublication>;
}
```

The interface is conceptual; exact implementation belongs in the architecture/codebase.

## Publishing Safety
Before an external side effect:
- validate account state;
- validate media;
- validate caption/metadata;
- verify the job is still executable;
- enforce idempotency;
- record an attempt.

## Platform Constraints
Do not assume that every Instagram feature can be automated. Feature availability can vary by account type, API version, permission, media format, and Meta policy. Requirements must be verified before implementation rather than encoded from assumptions.

## Failure Handling
- Authentication/permission failure → stop and surface reauthorization.
- Rate limit → retry with backoff according to provider guidance.
- Network timeout → retry if idempotency/operation safety permits.
- Invalid media/request → permanent failure requiring correction.
- Unknown provider error → preserve provider context and fail safely until classified.
