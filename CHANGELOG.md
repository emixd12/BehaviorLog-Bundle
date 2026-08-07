
# Changelog

## 0.2.0-draft

Driven by the first producer parity audit (Cadence, 2026-08). Every change is additive; 0.1.0-draft bundles remain valid.

Added:

- Definition History Profile: `data/behavior_definition_events.jsonl` with `behavior_definition_event` records (`baseline`/`revision`, `changed_fields`, full `previous`/`next` values). Renames and redefinitions were previously exportable only as app-private raw files.
- Time Tracking Profile: `data/time_sessions.jsonl` with `time_session` records. Durations are derived, never stored; export is opt-in; running sessions carry a null stop.
- Intervention rules: optional `data/intervention_rules.jsonl` records the standing reminder configuration (`channel`, `enabled`, signed `offset_minutes`), giving `Intervention.rule_id` a referent.
- `Intervention.failure_reason`: sanitized failure text for `failed` deliveries. Producers needed this and were forced into nonconforming fields without it.
- `privacy.contains_time_tracking` flag (optional).
- Metric vocabulary: `tracked_duration_total_seconds`, `tracked_duration_mean_seconds`.
- Canonical profile identifiers for `manifest.profiles`.
- `rules.definition_history_policy` (`event_sourced` | `untracked` | `immutable`). Absent means `untracked`, and readers must not assume current definitions applied at historical occurrences unless the policy is `immutable` or definition events cover the behavior.

Changed:

- Writers MUST map app-native pre-send delivery states (`pending`, `queued`, `scheduled`) to `planned`.
- Conformance now applies field discipline to profile records, not only core records: unknown top-level fields, missing required fields, and out-of-vocabulary enum values in any known `data/*.jsonl` file are errors.
- Reference validator enforces the profile-record checks above plus new semantic warnings (privacy flag pairing, running-session uniqueness, definition-history consistency, dangling `rule_id`, suspicious `failure_reason` content).
- `MAPPING.md` Cadence section rewritten against the real producer, including delivery-state and field-name mappings.

## 0.1.0-draft

Initial draft.

Added:

- Core bundle layout.
- App-neutral status vocabulary: `unresolved`, `completed`, `not_completed`.
- Required core files.
- Optional profiles for interventions, context, reviews, analytics, research, and mappings.
- JSON Schema draft file.
- Synthetic examples.
- Zero-dependency reference validator.
- Privacy and conformance guidance.
