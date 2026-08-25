# Veyra — FUTURE Integration

## Purpose
FUTURE is an independent Historical Content Production Engine. It turns verified historical research into structured visual packages and rendered PNG assets. Veyra consumes those outputs for Instagram operations.

## Boundary
Veyra must not import FUTURE code, call its internal renderer, depend on its filesystem, or assume its database schema.

FUTURE exposes a service contract. Veyra acts as a client.

## Desired Workflow

```text
Veyra content
   ↓
Design request
   ↓
FUTURE /render-jobs
   ↓
queued
   ↓
rendering
   ↓
completed
   ↓
asset references
   ↓
Veyra asset validation
   ↓
Instagram publication
```

## Render Request
Conceptually:

```json
{
  "source": {"type": "veyra", "contentId": "content_123"},
  "format": "instagram-carousel",
  "content": {
    "title": "Example historical story",
    "slides": []
  },
  "design": {
    "template": "historical-editorial",
    "theme": "archival-dark",
    "aspectRatio": "4:5"
  },
  "output": {
    "format": "png",
    "scale": 3
  }
}
```

## Response Contract
The initial response should return a stable external job ID. Veyra can poll status or use a callback/webhook when the contract supports it.

Completed output should include:
- render job ID
- status
- asset IDs/URLs
- MIME type
- width/height
- slide ordering
- checksum where available
- renderer/version metadata where useful

## Reliability
- Veyra assigns an idempotency key to render requests.
- Render jobs have explicit terminal and retryable states.
- A failed FUTURE render must not become an Instagram publication.
- A completed render can be reused if its content/design inputs remain valid.

## Figma-like Direction
Long term, FUTURE may expose discoverable templates, themes, and design specifications so Veyra can select a design without knowing how it is rendered. This is an API-level design system, not a requirement for a visual editor in Veyra.

## Local Development
During early Veyra development, use a mock FUTURE adapter. Integration tests should verify the contract without requiring a live FUTURE deployment.

## Versioning
The FUTURE integration contract must be versioned independently from Veyra's internal modules. Breaking changes require explicit contract-version handling.
