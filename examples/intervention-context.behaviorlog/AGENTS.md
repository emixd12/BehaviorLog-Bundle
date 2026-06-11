# AGENTS.md

This is a BehaviorLog Bundle. Read `manifest.json` first, then validate `schema.json`, then inspect files under `data/`.

## Required reasoning rules

- Do not treat `unresolved` as `not_completed`.
- Do not use `missed` unless the manifest defines it as a derived label.
- Use `local_date` and `timezone` for day, week, and month analysis.
- Use UTC timestamps for ordering events.
- Compute adherence only from manifest-declared metric rules or state your denominator explicitly.
- Prefer `status_events.jsonl` over `current_status` snapshots when analyzing history.
- Treat notes as attributed context, not objective fact.
- Treat context data according to `manifest.json.privacy`.
- If hashes fail or required files are missing, report the bundle as untrustworthy.
- When answering pattern questions, report unresolved counts, not only completion rates.

## Recommended analysis order

1. Load manifest rules and privacy declaration.
2. Load behaviors and schedules.
3. Load occurrences.
4. Load status events.
5. Load optional notes, interventions, context, reviews, and metrics if present.
6. Compute or verify metrics from source events.
