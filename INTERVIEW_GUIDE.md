# Interview guide

## 30-second version

BullSignal is a larger integration-heavy project. The public case focuses on three reliability problems I worked through: **duplicate events, stale data and deployment-state drift**. The main architectural response was to make system state explicit: stable operation identity for idempotency, freshness contracts instead of treating cache presence as health, and deployment flows that verify the target baseline before mutation and verify behaviour after apply or rollback.

## 2-minute version

The product has multiple boundaries: web/Telegram interfaces, backend services, external APIs, background workers, runtime cache/state, monitoring and a separate analytics research area.

The difficult failures were usually not “one function throws an exception.” They happened between components. For example, an event may be delivered twice, a cache may contain old data while the API still returns `200`, or a change may be prepared against a repository snapshot that no longer matches the deployed baseline.

So I modelled those states explicitly:

- idempotency key / prior-safe outcome for duplicate delivery;
- `fresh / stale / unavailable / degraded` for data health;
- `inspect → baseline → checkpoint → apply → verify → rollback` for deployment;
- monitoring lifecycle that closes only after recovery verification;
- research outputs cannot directly create production side effects.

## 5-minute route

1. Open `SYSTEM_CONTEXT.md` and explain component responsibilities.
2. Open the duplicate callback sequence in `DIAGRAMS.md`.
3. Show `DATA_FRESHNESS.md` and explain why cache hit != fresh data.
4. Show `RELIABILITY_CASES.md` for mini-postmortems.
5. Show `DEPLOYMENT_SAFETY.md` and explain baseline verification / rollback.
6. Finish with `TRACEABILITY.md` to connect requirements to verification evidence.

## Likely questions

### What is idempotency?
Repeating the same logical operation produces no additional business side effect after the first successful completion. It matters at retry/at-least-once boundaries.

### Why isn't HTTP 200 enough for health?
Because the endpoint can be reachable while the underlying data is stale or the frontend cannot render the intended state. Availability, freshness and user-visible health are different signals.

### Why verify the deployed baseline?
A correct patch against one version may be unsafe against a different deployed state. The target state should be checked before mutation instead of assumed.

### Why not retry every failed request?
A retry is safe only when the operation has known identity/semantics. Blindly retrying a state-changing request after an ambiguous failure can duplicate the action.

### Why isolate analytics research?
Experiments optimize/evaluate ideas; production has separate operational and safety constraints. Good research evidence is not authorization to mutate live state.

### What would you improve next?
Create a more formal SLI/SLO catalogue, expand contract-level tests around freshness/idempotency, and define a consistent audit-event model across user operations, incidents and deployments.

## Important honesty boundary

The public repository is an architecture/reliability case derived from a private project. Do not claim that the generic public JSON endpoints or diagrams are exact production topology; they are deliberately sanitized models of the engineering decisions.
