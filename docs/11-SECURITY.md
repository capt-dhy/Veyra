# Veyra — Security

## Threat Model
Veyra handles OAuth credentials, publishing permissions, user-generated content, media, and internal service credentials. Assume external API responses, uploaded media metadata, and user input are untrusted.

## Secrets
- Never commit secrets.
- Use environment/secret management.
- Never log access tokens or authorization codes.
- Rotate/revoke credentials when necessary.

## Token Storage
If tokens must be persisted, encrypt sensitive token material using a managed key or equivalent secure mechanism. Application responses should expose only safe metadata.

## Authorization
Every account, content, job, and publication operation must be scoped to an authorized owner/tenant. Do not trust account IDs supplied by clients without authorization checks.

## Webhooks
If Veyra receives callbacks from FUTURE or Meta, verify authenticity/signatures where the provider supports them, reject malformed payloads, and make processing idempotent.

## Input Validation
Use runtime schemas at API boundaries. Validate URLs, MIME types, sizes, captions, scheduling times, identifiers, and enum values.

## Media Safety
Do not execute uploaded content. Restrict accepted MIME types and sizes and use safe object storage. Treat generated HTML/CSS or other render artifacts as untrusted unless they remain isolated within FUTURE.

## Service-to-Service Security
Veyra↔FUTURE communication should use authenticated service credentials over TLS. Credentials should be scoped to the minimum required operations.

## Auditability
Record security-relevant actions such as account connection, token changes, publication attempts, cancellations, and administrative actions without storing sensitive secret material.

## Compliance
Platform usage must follow applicable Meta/Instagram terms, permissions, and developer policies.
