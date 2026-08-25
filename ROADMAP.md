# Roadmap

The public repository is intentionally documentation/architecture-heavy. Next steps deepen the evidence rather than publishing private production implementation.

## Next 1 — SLI/SLO catalogue

Tracked in [Issue #1](https://github.com/blsoo/bullsignal-system-architecture/issues/1).

Define public-safe service indicators for:

- freshness-sensitive data;
- background-job execution;
- callback duplicate handling;
- incident recovery time/evidence;
- deployment verification.

The goal is to show how reliability requirements become measurable operational signals.

## Next 2 — Contract test matrix

Tracked in [Issue #2](https://github.com/blsoo/bullsignal-system-architecture/issues/2).

Add boundary-level tests for duplicate delivery, stale/missing data, dependency failures, expired confirmation, baseline conflict and failed verification/recovery.

## Next 3 — Unified audit-event model

Tracked in [Issue #3](https://github.com/blsoo/bullsignal-system-architecture/issues/3).

Define a common conceptual evidence model for user critical operations, background processing, incidents and deployment/recovery actions while preserving the difference between **request**, **decision**, **execution** and **verification**.

## What will stay private

- production code/history;
- infrastructure topology and privileged access paths;
- credentials/account details;
- private datasets;
- proprietary analytics/trading rules.

The Issues are real next steps; the repository does not manufacture old history to look larger than it is.
