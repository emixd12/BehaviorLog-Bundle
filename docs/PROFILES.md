
# Profiles

Profiles add scoped records or requirements to the BehaviorLog core.

## Core Profile

Required files:

```text
manifest.json
schema.json
README.md
AGENTS.md
data/behaviors.jsonl
data/schedules.jsonl
data/occurrences.jsonl
data/status_events.jsonl
```

## Intervention Profile

Adds:

```text
data/interventions.jsonl
```

Use for reminders, cues, notification delivery, snoozes, dismissals, suppressions, and burden analysis.

## Context Profile

Adds:

```text
data/context_snapshots.jsonl
```

Use for explicitly exported context such as calendar state, coarse location label, activity state, mood, energy, sleep, weather, or focus mode.

Context must include privacy and consent metadata.

## Review Profile

Adds:

```text
data/reviews.jsonl
```

Use for weekly/monthly reviews, reflections, barriers, and user-approved adjustments.

## Analytics Profile

Adds:

```text
data/derived_metrics.jsonl
```

Metrics must cite `rule_id` values declared in the manifest.

## Research Profile

Future profile for:

- experiments;
- randomization;
- decision points;
- tailoring variables;
- intervention options;
- proximal outcomes;
- advanced provenance;
- de-identification metadata.

## Mapping Profiles

Future profiles may define concrete imports/exports for:

- iCalendar;
- xAPI;
- FHIR/Open mHealth;
- PROV/RO-Crate;
- ActivityStreams;
- Todoist, Notion, Apple Reminders, Google Calendar, and other apps.
