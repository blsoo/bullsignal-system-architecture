# Integration contracts

The examples here are conceptual/public-safe contracts. They are not production endpoints.

## Event/callback envelope

```json
{
  "event_id": "evt-demo-001",
  "event_type": "user.action",
  "occurred_at": "2026-01-01T12:00:00Z",
  "actor_id": "synthetic-user",
  "action": "open_details",
  "payload": {
    "resource_id": "synthetic-resource"
  }
}
```

### Contract rules

- `event_id` is stable across retries of the same logical event;
- processing the same `event_id` twice must not repeat the business side effect;
- malformed/unsupported events fail before mutation;
- acknowledgement and business completion are separate concepts when processing is asynchronous.

## Freshness-aware response

```json
{
  "status": "fresh",
  "generated_at": "2026-01-01T12:00:20Z",
  "source_updated_at": "2026-01-01T12:00:00Z",
  "age_seconds": 20,
  "data": {
    "state": "synthetic"
  }
}
```

Possible `status` values in this public model:

- `fresh` — usable under the current freshness contract;
- `stale` — data exists but is too old;
- `unavailable` — required data is absent;
- `degraded` — data may be usable but a dependency/verification condition is unhealthy.

## Critical action proposal

```json
{
  "proposal_id": "proposal-demo-001",
  "status": "pending_confirmation",
  "created_at": "2026-01-01T12:00:00Z",
  "expires_at": "2026-01-01T12:05:00Z",
  "baseline_version": "baseline-demo-7",
  "summary": "Apply one bounded state change"
}
```

### Confirmation request

```json
{
  "proposal_id": "proposal-demo-001",
  "confirmation": true
}
```

### Error semantics

| Situation | Public model |
|---|---|
| malformed input | `400 invalid_request` |
| unknown resource | `404 not_found` |
| duplicate already completed | return prior-safe outcome / idempotent success |
| proposal expired | `409 stale_proposal` |
| baseline changed | `409 baseline_conflict` |
| validation failed | `422 validation_failed` |
| dependency unavailable | `503 dependency_unavailable` |
| data too stale | `503 stale_data` or explicit stale response depending on UI contract |

## Retry guidance

A client may retry only when the operation contract says retry is safe. Retryable operations require a stable idempotency key or an equivalent operation identity.

Blind retries of unknown state-changing actions are not considered safe.
