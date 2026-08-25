# Requirements and non-functional requirements

These requirements describe the public architecture case, not proprietary product/trading logic.

## Functional requirements

### FR-01 — Duplicate-safe event handling
The system shall prevent a retried or duplicated external event from repeating the same business side effect.

**Acceptance criteria**
- the same idempotency key does not execute the side effect twice;
- duplicate delivery receives a safe deterministic outcome;
- duplicate detection is observable in logs/metrics.

### FR-02 — Freshness-aware data delivery
The system shall distinguish missing, stale and fresh data.

**Acceptance criteria**
- every freshness-sensitive response can determine source/update age;
- stale data is not presented as healthy current data;
- user-visible errors distinguish unavailable from stale where useful.

### FR-03 — Confirmation-gated critical action
A state-changing critical operation shall require explicit confirmation after validation/preview.

**Acceptance criteria**
- preview does not mutate final state;
- expired/stale confirmation cannot execute the old plan;
- execution verifies the observable result.

### FR-04 — Health and incident state
The system shall classify health as more than HTTP reachability.

**Acceptance criteria**
- dependency/data/runtime warnings can produce degraded state;
- critical failures can become incidents;
- recovery is complete only after verification.

### FR-05 — Deployment recovery path
Every production-affecting deployment flow shall define a recoverable checkpoint and verification step.

**Acceptance criteria**
- baseline mismatch stops the normal apply path;
- verification failure has an explicit rollback/recovery path;
- recovery itself is verified.

### FR-06 — Research isolation
Research analytics shall not automatically trigger live production side effects.

**Acceptance criteria**
- research outputs are treated as evidence/candidates;
- production activation requires a separate reviewed decision;
- experimental failure cannot directly create a production action.

## Non-functional requirements

### NFR-01 — Auditability
For state-changing or incident-relevant events, evidence shall be sufficient to reconstruct what was requested, what decision was made and what final state was observed.

### NFR-02 — Fail closed
When required state is ambiguous, missing or stale beyond the allowed contract, the system shall stop rather than guess.

### NFR-03 — Idempotency
Retry-safe boundaries shall be explicit for callbacks, operations and scheduled processing where duplicate execution creates user-visible or data consequences.

### NFR-04 — Observability
Monitoring shall expose at least availability, freshness and execution/verification state for reliability-critical flows.

### NFR-05 — Separation of concerns
UI adapters, application rules, background jobs, monitoring and research shall have explicit responsibility boundaries.

### NFR-06 — Public information boundary
The public portfolio shall not expose production credentials, privileged endpoints, real infrastructure placement, private datasets or proprietary trading rules.

## Constraints

- external APIs are not assumed perfectly reliable;
- callback/event delivery can be at-least-once;
- background processing can be delayed;
- production state may drift from an old repository snapshot;
- UI success is not proof of current data.
