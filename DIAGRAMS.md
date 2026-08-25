# Architecture and reliability diagrams

All diagrams are deliberately generic and omit production topology.

## 1. Duplicate-safe callback

```mermaid
sequenceDiagram
    actor User
    participant UI as Telegram/Web UI
    participant API as Handler
    participant Guard as Idempotency Guard
    participant Service as Application Service
    participant DB as Storage

    User->>UI: action
    UI->>API: event/request
    API->>Guard: check idempotency key
    alt already processed
        Guard-->>API: prior outcome
        API-->>UI: safe acknowledgement
    else new
        Guard-->>API: execute
        API->>Service: validated command
        Service->>DB: persist business state
        DB-->>Service: committed
        Service-->>API: result
        API->>Guard: mark completed
        API-->>UI: response
    end
```

## 2. Freshness contract

```mermaid
flowchart TD
    SRC[External source] --> COL[Collector / job]
    COL --> STORE[(Cache / storage)]
    STORE --> EXISTS{Data exists?}
    EXISTS -- No --> UNAVAILABLE[Unavailable]
    EXISTS -- Yes --> AGE{Within freshness limit?}
    AGE -- No --> STALE[Stale]
    AGE -- Yes --> API[API response]
    API --> RENDER{UI rendered?}
    RENDER -- No --> UIERR[UI error]
    RENDER -- Yes --> HEALTHY[Healthy user-visible state]
```

## 3. Confirmation-gated action

```mermaid
stateDiagram-v2
    [*] --> prepared
    prepared --> rejected: validation failed
    prepared --> awaiting_confirmation: validation passed
    awaiting_confirmation --> cancelled: cancel/expire
    awaiting_confirmation --> executing: explicit confirm
    executing --> completed: verification passed
    executing --> failed: execution/verification failed
    failed --> recovery: recoverable
    recovery --> rolled_back: recovery verified
```

## 4. Deployment safety

```mermaid
flowchart LR
    C[Change] --> I[Inspect]
    I --> B[Verify baseline]
    B -->|mismatch| STOP[Stop]
    B -->|match| BK[Checkpoint / backup]
    BK --> A[Apply minimal change]
    A --> V[Verify behaviour]
    V -->|pass| DONE[Complete]
    V -->|fail| R[Rollback / recovery]
    R --> RV[Verify recovery]
```

## 5. Incident lifecycle

```mermaid
stateDiagram-v2
    [*] --> healthy
    healthy --> degraded: warning/evidence
    degraded --> healthy: recovered + verified
    degraded --> incident: escalation
    healthy --> incident: critical failure
    incident --> investigating: acknowledged
    investigating --> recovering: remediation chosen
    recovering --> healthy: verification passed
    recovering --> investigating: verification failed
```

## 6. Research isolation

```mermaid
flowchart LR
    DATA[Historical / collected data] --> LAB[Research pipeline]
    LAB --> EXP[Experiments]
    EXP --> EVID[Evaluation evidence]
    EVID --> DEC{Reviewed promotion?}
    DEC -- No --> LAB
    DEC -- Candidate --> DOC[Documented candidate]
    DOC -. no automatic live action .-> PROD[Production application]
```

## 7. Reliability evidence chain

```mermaid
flowchart LR
    REQ[Requirement] --> DESIGN[Boundary / contract]
    DESIGN --> FAILURE[Failure modes]
    FAILURE --> CHECK[Verification / monitoring]
    CHECK --> TEST[Test case]
    TEST --> INCIDENT[Operational evidence]
    INCIDENT -. feedback .-> REQ
```
