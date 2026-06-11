
# Privacy and Redaction

Behavior logs are sensitive because they describe routines, failures, health-adjacent practices, availability, location-like context, reminders, and self-reflection.

BehaviorLog therefore treats privacy as part of the bundle contract rather than as an external policy document.

## Required privacy declaration

Every bundle MUST include:

```json
{
  "privacy": {
    "redaction_level": "standard_redaction",
    "subject_id_strategy": "pseudonymous",
    "contains_notes": true,
    "contains_context": false,
    "contains_raw_location": false,
    "contains_health_data": false,
    "contains_ai_generated_content": false
  }
}
```

## Recommended export profiles

| Profile | Use | Default stance |
|---|---|---|
| `migration_only` | Move data to another app. | Include core records. Exclude context unless required. Notes optional. |
| `standard_redaction` | Default personal export. | Pseudonymous subject, notes included, no raw location. |
| `analysis_safe` | Share with an AI assistant. | Pseudonymous subject, coarse context only, sensitive fields labeled. |
| `anonymized` | Research or public sample. | Remove direct identifiers, reduce timestamps if needed, redact notes unless consented. |
| `full_fidelity` | Personal archive. | Preserve all selected data with explicit warning. |

## Field sensitivity levels

Fields and records MAY include:

```text
low
medium
high
restricted
```

Recommended defaults:

| Data | Default sensitivity |
|---|---|
| Behavior title | medium |
| Category | low |
| Success definition | medium |
| Status events | medium |
| User notes | high |
| Reminder delivery | medium |
| Reminder message content | high |
| Coarse context labels | high |
| Precise location | restricted |
| Raw sensor trail | restricted |
| Health or medication-adjacent data | high or restricted |

## Notes

Notes are valuable for agent interpretability but may contain private material. Writers SHOULD make notes toggleable in export UI.

If notes are exported, agents MUST treat them as attributed context. They MUST NOT treat them as objective fact without qualification.

## Context data

Context is optional. A conforming core bundle can contain no context data.

When context is exported:

- prefer coarse place labels over coordinates;
- prefer derived state over raw sensor streams;
- include `source`, `precision`, and `consent_scope`;
- label sensitive context;
- avoid exporting context unless explicitly requested.

## AI-generated content

AI-generated analysis, summaries, or inferred labels MUST be distinguishable from user-entered data. Recommended locations:

```text
analysis/
extensions/<namespace>/
data/reviews.jsonl with note_role: ai_generated
```

AI-generated content MUST NOT be placed in source event files in a way that makes it look like user action.

## Receiving application duties

A receiving application SHOULD:

1. Show the user the bundle privacy profile before import.
2. Preserve privacy labels where possible.
3. Avoid exposing notes or context in logs and telemetry.
4. Avoid training models on imported bundles unless separately consented.
5. Keep unmapped sensitive fields out of default UI.
6. Explain any destructive normalization before import.

## Redaction guidance

For `analysis_safe` exports:

- pseudonymize `subject.subject_id`;
- keep local dates unless calendar pattern itself is sensitive;
- round or remove precise times only when the analysis does not require timing;
- remove raw location;
- replace place labels with coarse labels such as `home`, `work`, `gym`, `other`;
- include unresolved counts and denominator rules;
- remove third-party names from notes where practical.

For `anonymized` exports:

- remove direct identifiers;
- consider shifting dates while preserving weekday structure;
- redact notes by default;
- remove rare behavior titles that identify the subject;
- remove source app IDs that can be traced back to a user account.
