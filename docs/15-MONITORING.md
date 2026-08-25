# Veyra — Monitoring

## Health
Expose readiness and liveness checks. Readiness should verify required dependencies needed to serve traffic; liveness should detect a stuck process without treating temporary external provider failures as process death.

## Structured Logs
Every request/job should carry a correlation/request ID. Worker logs should include job ID, job type, attempt, account-safe identifier, provider, duration, and outcome.

Never log tokens, authorization codes, or sensitive payloads.

## Metrics
Initial useful metrics:
- API request count/latency/error rate;
- queue depth;
- job execution count and duration;
- retry count;
- publication success/failure rate;
- FUTURE render success/failure/latency;
- provider rate-limit responses;
- worker heartbeat/availability.

## Alerts
Alert on sustained worker failure, growing queue backlog, unusual publication failure rate, dependency outage, and repeated authentication failures.

## Operational Dashboard
A future dashboard should allow an operator to answer: what is scheduled, what is running, what failed, why it failed, and what was published.

## Audit Trail
Important state changes should be persisted independently of ephemeral logs so publication history survives log rotation.
