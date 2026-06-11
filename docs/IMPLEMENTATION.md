
# Implementation Guide

This guide describes how an application should produce a BehaviorLog Bundle.

## Export pipeline

Recommended writer pipeline:

```text
load source records
normalize behaviors
normalize schedules
materialize occurrences
normalize status event history
attach notes
attach optional interventions/context/reviews
compute optional metrics from manifest rules
write JSONL files
write CSV views if requested
write schema.json
write bundle AGENTS.md
write README.md
write manifest with file hashes
validate bundle
zip if requested
```

## Normalization rules

### Statuses

Map app-specific statuses into the core vocabulary.

Examples:

| Source | BehaviorLog |
|---|---|
| done | completed |
| complete | completed |
| checked | completed |
| not_done | not_completed |
| failed_by_user | not_completed |
| pending | unresolved |
| no_response | unresolved |
| missed | unresolved unless explicit non-completion is proven |
| skipped | not_completed with reason, or profile-specific skip event |

### Schedules

Use `behaviorlog.calendar_simple.v1` when possible. Use the smallest recurrence profile that preserves meaning.

Do not emit natural-language recurrence as the only schedule representation. Store the original phrase under `source` or `extensions` if useful.

### Occurrences

Export one row per generated occurrence. Use stable IDs across repeated exports when possible.

If an occurrence was removed because a schedule changed, prefer `occurrence_state: cancelled` over deleting it from history, unless the user requested deletion or redaction.

### Status history

Status changes should be append-only. A correction should create a new event with `status_semantics: explicit_user_correction` and `revises_event_id` when known.

## Import pipeline

Recommended reader pipeline:

```text
read manifest
verify hashes
load schema
load behavior/schedule/occurrence/status files
validate core enums and references
apply app-specific import mapping
show privacy summary to user
import source facts
compute app-native views
```

A reader should not import optional profile data unless it understands the profile or can safely preserve it.

## Agent ingestion pipeline

Recommended AI-agent pipeline:

```text
read bundle AGENTS.md
read manifest rules and privacy declaration
validate required files
load behaviors and schedules
load occurrences and status events
load optional notes/interventions/context if relevant
compute metrics from source data unless derived metrics are explicitly requested
state denominator rules and unresolved counts in answers
```

## Minimal writer pseudocode

```ts
const bundle = createBehaviorLogBundle({
  schemaVersion: "0.1.0-draft",
  producer: appMetadata,
  subject: pseudonymousSubject,
  privacy: selectedPrivacyProfile,
});

bundle.writeJsonl("data/behaviors.jsonl", normalizeBehaviors(source.behaviors));
bundle.writeJsonl("data/schedules.jsonl", normalizeSchedules(source.schedules));
bundle.writeJsonl("data/occurrences.jsonl", materializeOccurrences(source));
bundle.writeJsonl("data/status_events.jsonl", normalizeStatusEvents(source.statusHistory));

if (includeNotes) bundle.writeJsonl("data/notes.jsonl", normalizeNotes(source.notes));
if (includeInterventions) bundle.writeJsonl("data/interventions.jsonl", normalizeInterventions(source.reminders));
if (includeContext) bundle.writeJsonl("data/context_snapshots.jsonl", normalizeContext(source.context));

bundle.writeSchema();
bundle.writeAgentsMd();
bundle.writeReadme();
bundle.writeManifestWithHashes();
bundle.validate();
```
