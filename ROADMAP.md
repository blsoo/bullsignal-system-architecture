# Roadmap

The public repository is intentionally documentation/architecture-heavy. Next steps deepen the evidence rather than publishing private production implementation.

## Next 1 — SLI/SLO catalogue

Define public-safe service indicators for:

- freshness-sensitive data;
- background-job execution;
- callback duplicate handling;
- incident recovery time/evidence;
- deployment verification.

The goal is to show how reliability requirements become measurable operational signals.

## Next 2 — Contract test matrix

Add a table of boundary-level tests for:

- same event delivered twice;
- stale data crossing threshold;
- dependency timeout with previous cache present;
- expired confirmation;
- baseline conflict;
- verification failure and recovery.

## Next 3 — Unified audit-event model

Define a common conceptual evidence model for:

- user critical operations;
- background processing;
- incidents;
- deployment/recovery actions.

The model must preserve the difference between **request**, **decision**, **execution** and **verification**.

## What will stay private

- production code/history;
- infrastructure topology and privileged access paths;
- credentials/account details;
- private datasets;
- proprietary analytics/trading rules.

Open GitHub Issues are used for the concrete roadmap so the repository has a real next-step workflow instead of manufactured history.
