
# BehaviorLog Bundle Specification

Version: `0.1.0-draft`  
Format identifier: `behaviorlog.bundle`  
Package extension: `.behaviorlog/` or `.behaviorlog.zip`

## 1. Status of this document

This document defines the BehaviorLog Bundle core profile. It is a draft intended for implementation feedback.

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are used in their standard normative sense.

## 2. Purpose

A BehaviorLog Bundle is a self-describing export package for behavior-adherence data. It is intended to let applications, researchers, users, and AI agents exchange recurring behavior logs without losing the rules that make those logs interpretable.

The core concern is adherence: whether a person completed a recurring intended behavior, explicitly did not complete it, or has not yet resolved the occurrence.

Adjacent concepts such as reminders, context, reviews, experiments, goals, projects, measurements, and clinical mappings are handled through optional profiles.

## 3. Package forms

A bundle MAY be represented as either:

```text
name.behaviorlog/
name.behaviorlog.zip
```

A zipped bundle MUST contain the same internal relative paths as the directory form. Readers MUST NOT require internet access to parse a valid core bundle.

## 4. Required core files

Every valid core bundle MUST contain:

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

Optional files include:

```text
data/notes.jsonl
data/interventions.jsonl
data/context_snapshots.jsonl
data/reviews.jsonl
data/derived_metrics.jsonl
csv/*.csv
raw/*
docs/*
```

## 5. Manifest

`manifest.json` is the reproducibility anchor. Readers MUST parse it before loading data records.

The manifest MUST include:

- `format`: exactly `behaviorlog.bundle`
- `schema_version`
- `exported_at_utc`
- `producer`
- `subject`
- `privacy`
- `rules`
- `files`

The manifest SHOULD include hashes for all required files. Writers SHOULD hash optional files when the optional file is included.

Each file entry MUST include:

```json
{
  "path": "data/status_events.jsonl",
  "media_type": "application/jsonl",
  "schema_ref": "#/$defs/StatusEvent",
  "required": true,
  "sha256": "..."
}
```

## 6. Encoding and data formats

JSON files MUST be UTF-8 encoded.

JSONL files MUST contain exactly one JSON object per line. Blank lines SHOULD NOT be emitted. Readers MAY ignore trailing blank lines but SHOULD warn.

CSV files are optional views. JSONL files are authoritative when CSV and JSONL disagree.

## 7. Extensions

Unknown fields are not allowed at top level of core records. Producers MAY include custom data only under an `extensions` object.

Extension keys SHOULD use one of these namespacing patterns:

```text
com.example.product
org.example.research_profile
https://example.org/behaviorlog/extensions/foo
```

A reader that does not understand an extension MUST ignore it unless the manifest declares it as required.

## 8. Identifiers

Record IDs MUST be stable within a bundle. Writers SHOULD preserve source IDs in `source.original_id` when importing from another system.

IDs MAY be UUIDs, deterministic IDs, or app-local IDs. They MUST be strings and MUST NOT expose personally identifying information unless the export profile intentionally includes identifiable data.

## 9. Time model

BehaviorLog separates ordering time from behavioral day.

Records that describe events SHOULD include:

- `*_at_utc` for absolute ordering
- `local_date` for the user's behavioral day
- `local_time` when relevant
- `timezone` as an IANA time zone name
- `utc_offset_at_event` when relevant

Timestamps SHOULD use RFC 3339 format. Time zone names SHOULD use the IANA Time Zone Database.

A reader MUST use `local_date` and `timezone` for day, week, month, and adherence-window analysis. A reader MUST use UTC instants for ordering events.

## 10. Core status vocabulary

The only core occurrence statuses are:

```text
unresolved
completed
not_completed
```

Definitions:

| Status | Meaning |
|---|---|
| `unresolved` | No explicit completion or non-completion decision has been recorded for the occurrence. |
| `completed` | The occurrence was explicitly completed by the user or by a manifest-declared high-confidence source. |
| `not_completed` | The occurrence was explicitly marked not completed by the user or by a manifest-declared high-confidence source. |

Rules:

- A writer MUST NOT store `missed` as a core status.
- A writer MUST NOT convert silence into `not_completed`.
- `unresolved` MUST NOT be counted as `not_completed`.
- `skipped` is not a core status. It MAY appear as a reason code or optional profile concept.
- `snoozed` is not a core status. It belongs to the Intervention Profile.
- `cancelled` is not a core status. It belongs to `occurrence_state`.

## 11. Occurrence state

`occurrence_state` describes whether the occurrence is eligible to count in adherence denominators.

Core values:

```text
active
cancelled
```

`active` occurrences are eligible unless a metric rule excludes them. `cancelled` occurrences SHOULD be excluded from adherence denominators unless a manifest rule states otherwise.

Deletion and legal/privacy erasure SHOULD be handled through redaction, omission, or a future tombstone profile, not by inventing a status.

## 12. Source and provenance

Every core record SHOULD include `source`. Status events MUST include `source`.

A source object SHOULD include:

```json
{
  "producer": "ExampleApp",
  "producer_version": "1.2.0",
  "original_id": "abc123",
  "capture_method": "manual_tap",
  "imported_from": null,
  "confidence": "high"
}
```

Allowed `capture_method` values include:

```text
manual_tap
manual_text
system_generated
imported
inferred
derived
ai_generated
unknown
```

Source confidence values:

```text
high
medium
low
ambiguous
unknown
```

A writer MUST NOT represent AI-generated interpretation as original user action. AI-generated analysis belongs in an optional analysis output or extension namespace.

## 13. Behavior records

`data/behaviors.jsonl` contains one `Behavior` record per behavior.

Required fields:

```json
{
  "record_type": "behavior",
  "behavior_id": "beh_001",
  "title": "Brush teeth",
  "category": "hygiene",
  "success_definition": "Brush teeth once during the evening window.",
  "created_at_utc": "2026-06-01T18:00:00Z"
}
```

Required behavior fields:

- `record_type`
- `behavior_id`
- `title`
- `category`
- `success_definition`
- `created_at_utc`

`description`, `archived_at_utc`, `expected_duration_minutes`, `source`, and `extensions` are optional.

`category` is free text but SHOULD use a recommended category when possible:

```text
health_wellness
fitness
hygiene
medication_non_dose
sleep
nutrition
work
learning
chores
admin
creative
social
finance
caregiving
avoidance
reflection
other
uncategorized
```

`success_definition` SHOULD be plain language. Applications SHOULD avoid over-modeling open-ended motivations, barriers, implementation intentions, and avoidance descriptions in the core schema. These can be recorded in notes or optional profiles.

## 14. Schedule records

`data/schedules.jsonl` contains one `Schedule` record per schedule rule.

Required fields:

```json
{
  "record_type": "schedule",
  "schedule_id": "sch_001",
  "behavior_id": "beh_001",
  "recurrence_profile": "behaviorlog.calendar_simple.v1",
  "recurrence": { "type": "daily", "interval": 1 },
  "timezone": "America/Los_Angeles",
  "active_from_local_date": "2026-06-01"
}
```

Core recurrence profile:

```text
behaviorlog.calendar_simple.v1
```

The simple profile supports:

```text
daily
every_n_days
weekly_on_weekdays
every_n_weeks_on_weekdays
monthly_on_day
```

For `monthly_on_day`, if a month lacks the requested day, the occurrence SHOULD fall back to the last day of that month unless the schedule declares another policy.

Optional schedule profiles MAY include:

```text
behaviorlog.completion_interval.v1
rfc5545.rrule
```

Natural-language recurrence is out of scope for core bundles.

Schedule changes SHOULD be represented by ending the old schedule with `active_until_local_date` and creating a new schedule. Occurrences SHOULD retain the `schedule_id` used to generate them.

## 15. Occurrence records

`data/occurrences.jsonl` contains one `Occurrence` record per generated scheduled instance.

Required fields:

```json
{
  "record_type": "occurrence",
  "occurrence_id": "occ_001",
  "behavior_id": "beh_001",
  "schedule_id": "sch_001",
  "scheduled_for_utc": "2026-06-12T04:30:00Z",
  "local_date": "2026-06-11",
  "timezone": "America/Los_Angeles",
  "occurrence_state": "active",
  "current_status": "unresolved"
}
```

Writers SHOULD materialize past and current scheduled instances. Writers MAY include future occurrences, but MUST make them distinguishable by `scheduled_for_utc`, `local_date`, and status.

`current_status` is a convenience snapshot. `status_events.jsonl` is authoritative for history.

## 16. Status event records

`data/status_events.jsonl` contains append-only status events.

Required fields:

```json
{
  "record_type": "status_event",
  "event_id": "evt_001",
  "occurrence_id": "occ_001",
  "behavior_id": "beh_001",
  "status": "completed",
  "status_semantics": "explicit_user_mark",
  "recorded_at_utc": "2026-06-12T04:44:03Z",
  "effective_at_utc": "2026-06-12T04:43:55Z",
  "local_date": "2026-06-11",
  "timezone": "America/Los_Angeles",
  "source": { "capture_method": "manual_tap", "confidence": "high" }
}
```

Allowed `status_semantics` values:

```text
explicit_user_mark
explicit_user_correction
imported_explicit
system_rule_declared
ambiguous_import
```

A writer MUST use `ambiguous_import` when a source status cannot be safely normalized. A writer SHOULD map ambiguous source states such as `missed` to `unresolved` unless the source proves explicit non-completion.

A user correction MUST append a new status event. It SHOULD include `revises_event_id` when correcting a prior event.

## 17. Notes

`data/notes.jsonl` is optional but recommended for agent intelligibility.

Notes MAY attach to:

```text
behavior
occurrence
status_event
review
```

Notes SHOULD be stored as Markdown text in `body_markdown`, even if the source app only accepts plain text.

Allowed `note_role` values:

```text
user
imported
system
ai_generated
```

Agents MUST treat notes as attributed context, not objective fact.

## 18. Intervention Profile

`data/interventions.jsonl` is optional. It records prompts, reminders, notifications, nudges, suppressions, snoozes, dismissals, delivery failures, and related burden signals.

A minimal intervention record includes:

```json
{
  "record_type": "intervention",
  "intervention_id": "int_001",
  "behavior_id": "beh_001",
  "occurrence_id": "occ_001",
  "intervention_type": "reminder",
  "channel": "browser_push",
  "planned_for_utc": "2026-06-12T04:15:00Z",
  "delivery_status": "sent"
}
```

Core recognized channels:

```text
browser_push
email
sms
mobile_push
in_app
calendar_notification
voice_assistant
webhook
other
```

Reminder message content SHOULD be excluded by default unless the export profile intentionally includes it. Writers SHOULD prefer `message_variant` and `rule_id`.

## 19. Context Profile

`data/context_snapshots.jsonl` is optional. Context MUST NOT be exported unless the user or implementer explicitly selects a context-enabled export profile.

Context values SHOULD include:

- `source`
- `precision`
- `consent_scope`
- `captured_at_utc`
- `timezone`

Default exports SHOULD use coarse context labels. Precise GPS, raw sensor trails, and invasive telemetry SHOULD be excluded from v0.1 core exports.

## 20. Review Profile

`data/reviews.jsonl` is optional. Reviews may contain user reflections, summary metrics shown to the user, barriers, adjustments, and review-period notes.

AI-generated reviews MUST be labeled as `ai_generated` and MUST NOT overwrite user-authored reviews.

## 21. Analytics Profile and metric vocabulary

`data/derived_metrics.jsonl` is optional. Every metric record MUST include `rule_id`, and the referenced rule MUST be declared in `manifest.json.rules.metric_rules`.

Core metric vocabulary:

| Metric | Formula |
|---|---|
| `explicit_adherence_rate` | `completed / (completed + not_completed)` |
| `resolution_rate` | `(completed + not_completed) / eligible_occurrences` |
| `scheduled_completion_rate` | `completed / eligible_occurrences` |
| `unresolved_rate` | `unresolved / eligible_occurrences` |
| `on_time_completion_rate` | `on_time_completed / completed` |
| `schedule_slippage_minutes` | `actual_completion_time - scheduled_time` |

Intervention Profile metrics:

| Metric | Formula |
|---|---|
| `reminder_response_rate` | `completions_after_reminder / reminders_delivered` |
| `intervention_burden_index` | Manifest-declared weighted burden score. |

A metric record MUST include period boundaries, timezone, included behavior IDs, numerator, denominator, unresolved count where applicable, and rule ID.

Agents MUST NOT compare adherence rates unless denominator rules match.

## 22. Privacy

Every bundle MUST declare a privacy profile in `manifest.json.privacy.redaction_level`.

Recommended redaction levels:

```text
full_fidelity
standard_redaction
analysis_safe
anonymized
migration_only
```

The default recommended export is `standard_redaction`. Subject identity SHOULD be pseudonymous by default.

## 23. Reader behavior

A conforming reader MUST:

1. Read `manifest.json` first.
2. Validate required file presence.
3. Verify hashes when present.
4. Validate JSON/JSONL parseability.
5. Reject unknown core statuses.
6. Preserve `unresolved` semantics.
7. Use local date and timezone for calendar-period analysis.
8. Distinguish source records from derived metrics.
9. Ignore unknown optional extensions unless declared required.
10. Report privacy labels before exposing sensitive data.

## 24. Writer behavior

A conforming writer MUST:

1. Emit required core files.
2. Emit normalized records.
3. Put custom data under `extensions`.
4. Preserve status event history.
5. Declare mapping choices for imported statuses.
6. Include a compact `AGENTS.md` in the bundle.
7. Include a self-contained `schema.json`.
8. Emit an app-neutral vocabulary, with app-specific terms only in mappings or source fields.

## 25. Non-goals for v0.1

Core v0.1 does not define:

- Medical dosing semantics
- Clinical adherence claims
- Multi-user/team tracking
- Gamification economies
- Raw passive surveillance exports
- Automatic causal inference
- Natural-language recurrence
- Required Apple Health, Google Fit, FHIR, or calendar sync

These may be addressed through future profiles.
