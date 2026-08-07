
# Profiles

Profiles add scoped records or requirements to the BehaviorLog core.

The manifest `profiles` array SHOULD name each profile a bundle uses, with the canonical identifiers shown below. Notes are part of the optional core surface and have no profile identifier.

## Core Profile

Identifier: `core`

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

Identifier: `intervention`

Adds:

```text
data/interventions.jsonl
data/intervention_rules.jsonl   (optional)
```

Use for reminders, cues, notification delivery, snoozes, dismissals, suppressions, and burden analysis.

`interventions.jsonl` records delivery events. `intervention_rules.jsonl` records the standing configuration that generates them — per-behavior channels, enablement, and send offsets — so a receiving app can rebuild the user's cue setup, not just replay past deliveries.

## Definition History Profile

Identifier: `definition_history`

Adds:

```text
data/behavior_definition_events.jsonl
```

Use for append-only revisions to behavior titles, descriptions, categories, and success definitions. Renames change how past occurrences should be interpreted; this profile keeps that interpretive trail portable.

Bundles exporting this profile SHOULD declare `rules.definition_history_policy: event_sourced` in the manifest.

Historical definitions can be sensitive. Disclose their inclusion the same way notes are disclosed.

## Time Tracking Profile

Identifier: `time_tracking`

Adds:

```text
data/time_sessions.jsonl
```

Use for start/stop elapsed-time sessions tracked against occurrences. Duration is always derived from the interval, never stored. Sessions are effort measurements, not completion decisions.

Exact session timestamps reveal activity patterns, so this profile is export-opt-in and pairs with the `contains_time_tracking` privacy flag.

## Context Profile

Identifier: `context`

Adds:

```text
data/context_snapshots.jsonl
```

Use for explicitly exported context such as calendar state, coarse location label, activity state, mood, energy, sleep, weather, or focus mode.

Context must include privacy and consent metadata.

## Review Profile

Identifier: `review`

Adds:

```text
data/reviews.jsonl
```

Use for weekly/monthly reviews, reflections, barriers, and user-approved adjustments.

## Analytics Profile

Identifier: `analytics`

Adds:

```text
data/derived_metrics.jsonl
```

Metrics must cite `rule_id` values declared in the manifest.

## Research Profile

Identifier: `research`

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
