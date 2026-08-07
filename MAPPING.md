
# Mapping Notes

This document sketches mappings between BehaviorLog Bundle and adjacent standards or app models. These mappings are not normative unless a future profile adopts them.

## Cadence-style apps

Cadence is the reference producer. Its internal status-event table mirrors the StatusEvent record one-to-one, so the core loop maps directly. The richer surfaces map as follows.

| Cadence concept | BehaviorLog concept |
|---|---|
| Behavior | Behavior |
| Category (display name) | `behavior.category` canonical value; display name and category ID under extensions |
| Schedule (recurrence + N time slots) | One Schedule record per time slot, sharing the recurrence |
| Exact time slot | `local_time` |
| Range slot / preset | `window_start_local` + `window_end_local`; preset label under extensions |
| Occurrence | Occurrence |
| `unresolved` / `completed` / `not_completed` | Same core vocabulary |
| Status snapshot timestamps (`completed_at`, `status_marked_at`) | Latest status event's `effective_at_utc` / `recorded_at_utc` |
| Status event history, corrections, clear-decision | StatusEvent records, including `status: "unresolved"` corrections |
| Needs decision UI group | Derived view from unresolved prior local dates |
| Behavior title/description revisions | Definition History Profile |
| Elapsed-time sessions | Time Tracking Profile |
| Per-behavior reminder settings | Intervention rules (`data/intervention_rules.jsonl`) |
| Browser/email reminder deliveries | Intervention records |
| Occurrence note | Note attached to occurrence |
| Export resolver | Bundle writer |

Mapping rules:

- App-native status terms such as `done/not_done` are source terms. Public records use `completed/not_completed`.
- App-native delivery state `pending` maps to `planned`. App field `scheduled_send_at` maps to `planned_for_utc`.
- Sanitized delivery error text maps to `failure_reason`.
- A nonnegative "remind N minutes before" offset maps to a negative `offset_minutes` on the intervention rule.
- Display categories map to the closest canonical category; keep the display name in extensions rather than widening the canonical list.
- When an app does not collect a required field such as `success_definition`, the writer MAY synthesize a neutral value, and SHOULD mark the record's `source.capture_method` accordingly so agents do not read producer boilerplate as user intent.
- Schedule-slot fan-out: when one recurrence has several time slots, emit one Schedule per slot. Keep any parent grouping ID under extensions if round-tripping the grouping matters.
- Category registries (user-defined category lists with ordering) stay app-side. The canonical `category` string plus extensions is the portable surface; a bundle does not carry empty categories.

## CSV

CSV is a view, not the canonical representation.

Recommended CSV files:

```text
csv/behaviors.csv
csv/schedules.csv
csv/occurrences.csv
csv/status_events.csv
```

CSV rows SHOULD preserve IDs so records can be joined back to JSONL records.

Nested objects such as recurrence, source, privacy, and extensions SHOULD be stringified as JSON only if needed. A CSV-only import is partial unless accompanied by manifest and schema metadata.

## iCalendar / RFC 5545

BehaviorLog schedules can map to iCalendar recurrence rules when the schedule uses `recurrence_profile: rfc5545.rrule`.

Potential mapping:

| BehaviorLog | iCalendar |
|---|---|
| Schedule | VEVENT or VTODO recurrence source |
| `recurrence` | RRULE/RDATE/EXDATE |
| `timezone` | VTIMEZONE / TZID |
| Occurrence | Expanded recurrence instance |

BehaviorLog core keeps a simpler recurrence profile because unrestricted RRULE can be more expressive than many adherence apps need.

## xAPI

xAPI statements can express status events as actor-verb-object records.

Potential mapping:

| BehaviorLog | xAPI |
|---|---|
| Subject | Actor |
| Status event | Statement |
| `completed` | Verb: completed |
| `not_completed` | Verb: marked-not-completed |
| Occurrence | Object |
| Schedule/window/context | Context |
| Status details | Result |

BehaviorLog still needs its own schedule and adherence semantics because xAPI does not define denominator rules for recurring adherence.

## FHIR and Open mHealth

FHIR and Open mHealth mappings are optional clinical or health-data bridge profiles. They are not core.

Potential FHIR mapping:

| BehaviorLog | FHIR |
|---|---|
| Behavior | Goal, CarePlan activity, or PlanDefinition-derived activity |
| Schedule | CarePlan/Task timing or occurrence timing |
| Occurrence | Task |
| Status event | Task status update or Observation |
| Derived metric | Observation |

Clinical apps SHOULD add domain-specific safety semantics. BehaviorLog core MUST NOT be treated as a medical dosing or clinical adherence standard by itself.

## W3C PROV

BehaviorLog `source` objects map to provenance concepts.

| BehaviorLog | PROV |
|---|---|
| Record | Entity |
| Export process | Activity |
| Producer app/user/importer | Agent |
| Source import | Derivation |
| Metric computation | Activity using source entities |

A future Research Profile may define a formal PROV export.

## RO-Crate

RO-Crate can wrap BehaviorLog bundles for research distribution, archival, publication, and richer metadata.

Potential mapping:

- BehaviorLog bundle as dataset;
- JSONL files as data entities;
- schema, validator, and scripts as software/context entities;
- research protocol as metadata.

## ActivityStreams

ActivityStreams can represent completed or potential activities, but it is too broad and social by default for BehaviorLog core.

Potential mapping:

| BehaviorLog | ActivityStreams |
|---|---|
| Status event | Activity |
| Subject | Actor |
| Behavior/Occurrence | Object |
| Completed/not completed | Activity type or extension vocabulary |

BehaviorLog should not adopt ActivityStreams as its core because adherence requires recurrence, unresolved, intervention, and denominator semantics.

## Future app mappings

Likely future mappings include:

- Apple Reminders
- Google Calendar
- Todoist
- Notion
- Apple Health / Google Fit
- generic habit trackers
- research data packages

App-specific mappings SHOULD live in separate profile documents or extension packages.
