
# Suggested Diagrams

These diagrams are intentionally not rendered in the core spec. They are useful additions for a GitHub README or documentation site.

## Bundle structure

```mermaid
flowchart TD
    A[BehaviorLog Bundle] --> B[manifest.json]
    A --> C[schema.json]
    A --> D[AGENTS.md]
    A --> E[data]
    E --> F[behaviors.jsonl]
    E --> G[schedules.jsonl]
    E --> H[occurrences.jsonl]
    E --> I[status_events.jsonl]
    E --> J[optional profiles]
```

## Core data chain

```mermaid
flowchart LR
    B[Behavior] --> S[Schedule]
    S --> O[Occurrence]
    O --> E[Status Event]
    E --> M[Metric]
    I[Intervention] -. optional .-> O
    C[Context Snapshot] -. optional .-> E
```

## Agent reading flow

```mermaid
flowchart TD
    A[Read AGENTS.md] --> B[Read manifest]
    B --> C[Validate schema and hashes]
    C --> D[Load core JSONL]
    D --> E[Load optional profiles]
    E --> F[Compute metrics with declared rules]
    F --> G[Report unresolved counts and privacy limits]
```
