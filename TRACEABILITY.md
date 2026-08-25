# Traceability matrix

| Requirement | Design evidence | Failure mode | Verification evidence |
|---|---|---|---|
| FR-01 duplicate-safe event handling | `DIAGRAMS.md` duplicate callback; `INTEGRATION_CONTRACTS.md` event identity | repeated side effect | repeat same event key, observe one business action |
| FR-02 freshness-aware data | `DATA_FRESHNESS.md` | stale cache presented as current | cross age threshold, expect stale state; restore fresh update |
| FR-03 confirmation gate | `DIAGRAMS.md` confirmation state machine | stale/expired proposal executes | expiry/baseline conflict tests |
| FR-04 health/incident state | `INCIDENT_MODEL.md` | warning disappears without recovered behaviour | verify capability before incident closure |
| FR-05 deployment recovery | `DEPLOYMENT_SAFETY.md` | wrong baseline or failed apply | baseline mismatch stops; rollback recovery check |
| FR-06 research isolation | `RESEARCH_BOUNDARY.md` | experiment triggers live action | research output has no direct production mutation path |
| NFR-01 auditability | incident/deployment evidence model | cannot reconstruct outcome | terminal state includes request/decision/verification evidence |
| NFR-02 fail closed | freshness + baseline rules | system guesses under ambiguity | missing/stale/unknown state stops bounded action |
| NFR-03 idempotency | event contract + reliability case | retry duplicates operation | repeated identity produces prior-safe result |
| NFR-04 observability | freshness + incident docs | HTTP health hides stale data | availability/freshness/execution are separate signals |
| NFR-05 separation of concerns | `SYSTEM_CONTEXT.md` | UI or research owns production rules | boundary review and architecture checks |
| NFR-06 public boundary | repository CI validator | secret/private topology committed | automated public-safety check |

## Why traceability is useful here

The repository is not intended to say only “I know idempotency” or “I know monitoring.” A reviewer can start at a requirement, see the architectural response, inspect the associated failure mode and identify how the behaviour should be verified.

That is the same chain used in the other portfolio projects:

`requirement → rule/boundary → interface/model → failure scenario → test/evidence`
