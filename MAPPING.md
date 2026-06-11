
# Mapping Notes

This document sketches mappings between BehaviorLog Bundle and adjacent standards or app models. These mappings are not normative unless a future profile adopts them.

## Cadence-style apps

A simple Cadence-like tracker maps cleanly to the core.

| Cadence concept | BehaviorLog concept |
|---|---|
| Behavior | Behavior |
| Recurrence rule | Schedule |
| Occurrence | Occurrence |
| `unresolved` | `unresolved` |
| `done` | `completed` |
| `not_done` | `not_completed` |
| Needs decision UI group | Derived view from unresolved prior local dates |
| Browser/email reminder delivery | Intervention Profile |
| Occurrence note | Note attached to occurrence |
| Export resolver | Bundle writer |

Mapping rule: `done/not_done` are app-specific source terms. Public BehaviorLog records SHOULD use `completed/not_completed`.

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
