# BullSignal — engineering case study

This document describes engineering work around BullSignal without exposing production-sensitive details.

## Context

BullSignal evolved from a web product into a system with several interacting parts: backend flows, Telegram UX, external APIs, background jobs, runtime caches, monitoring, deployment tooling and analytics research.

The main engineering challenge became **change safety across boundaries**: a component could work in isolation while the user-visible system still failed because of stale data, duplicate events, drifted deployed state or an unhealthy dependency.

## Engineering focus

### Integration boundaries

- command/callback routing;
- API-backed user actions;
- duplicate-event protection;
- separation of user-facing areas and service responsibilities;
- explicit confirmation for actions with side effects.

### Reliability

- health and incident states;
- freshness-aware data delivery;
- fail-closed behaviour when required state is missing or ambiguous;
- bounded retries where they are safe;
- verification after state-changing operations;
- rollback paths designed before deployment.

### Deployment

A deployment is treated as a state transition, not a file copy.

```text
inspect change
→ verify expected baseline
→ create recoverable checkpoint
→ apply minimal change
→ verify behaviour
→ complete or rollback
```

### Research isolation

Analytics experiments are separated from live side effects. Research artifacts may inform reviewed decisions, but they do not automatically activate production behaviour.

## Key lessons

- integration failures often happen at boundaries rather than inside one function;
- idempotency is a system requirement, not a Telegram-specific workaround;
- data availability and data freshness are different states;
- deployed state must be verified instead of inferred from an old repository snapshot;
- rollback and recovery verification belong to normal design;
- logs should answer both **what happened?** and **what changed?**;
- a prototype becoming larger is not the same thing as becoming maintainable.

## Stack touched

PHP, Python, SQL, REST/HTTP, JSON, Telegram Bot API, Linux, Git/GitHub, CI/CD, shell automation, external API integration and web-platform integration.

## Public boundary

The production implementation stays private. This repository intentionally omits credentials, hosts, provider/account details, privileged endpoints, deployment paths, private datasets and trading logic.
