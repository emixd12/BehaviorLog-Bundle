
# Conformance

BehaviorLog conformance is defined for writers, readers, validators, and bundles.

## Bundle levels

| Level | Name | Requirements |
|---|---|---|
| 0 | Partial readable export | Not a conforming core bundle. Some BehaviorLog-shaped files may be present. |
| 1 | Core bundle | All required files, valid core records, valid manifest, app-neutral status vocabulary. |
| 2 | Core + CSV views | Level 1 plus CSV migration views. |
| 3 | Intervention profile | Level 1 plus valid `interventions.jsonl`. |
| 4 | Context/profile privacy | Level 1 plus valid context records and explicit privacy labels. |
| 5 | Research-grade | Level 1 plus provenance, analytics, experiment metadata, or RO-Crate/PROV mapping. |

## Core bundle errors

A validator MUST fail a core bundle when:

- `manifest.json` is missing;
- `schema.json` is missing;
- `AGENTS.md` is missing;
- any required data file is missing;
- JSON or JSONL cannot be parsed;
- a core record uses an unknown top-level field outside `extensions`;
- a status is not one of `unresolved`, `completed`, or `not_completed`;
- `missed` is emitted as a core status;
- a status event references a missing occurrence;
- an occurrence references a missing behavior or schedule;
- a schedule references a missing behavior;
- required file hashes are present and do not match;
- a derived metric uses a `rule_id` not declared in the manifest.

## Warnings

A validator SHOULD warn when:

- optional file hashes are absent;
- category is `uncategorized` for many records;
- many old occurrences remain unresolved;
- `ambiguous_import` appears in status events;
- notes are included without a privacy warning;
- context is included without `precision` or `consent_scope`;
- future occurrences are exported without a clear generation horizon;
- derived metrics omit unresolved counts;
- extension namespaces are not clearly namespaced.

## Writer conformance

A core writer MUST:

1. Emit required files.
2. Emit normalized app-neutral records.
3. Use only the core status vocabulary.
4. Preserve unresolved as unresolved.
5. Use append-only status event history.
6. Include local dates and IANA time zones.
7. Place custom fields under `extensions`.
8. Include a manifest privacy declaration.
9. Include a self-contained schema copy.
10. Include compact bundle-level agent instructions.

A writer SHOULD:

- hash all emitted files;
- include source provenance;
- include CSV views when useful for migration;
- include mapping notes for imported statuses;
- preserve original IDs in `source.original_id`;
- avoid exporting context by default.

## Reader conformance

A core reader MUST:

1. Load `manifest.json` first.
2. Respect privacy labels.
3. Validate or reject unknown core statuses.
4. Use UTC instants for ordering.
5. Use local dates and time zones for behavior-day analysis.
6. Treat JSONL as authoritative over CSV views.
7. Treat `current_status` as a snapshot, not authoritative history.
8. Ignore unknown optional extensions unless declared required.
9. Report unresolved counts when computing adherence.
10. State denominator rules when reporting metrics.

A reader MUST NOT:

- convert silence into failure;
- call unresolved items missed unless a manifest rule defines that derived label;
- infer causal effects from ordinary logs;
- expose sensitive notes/context without checking privacy declarations;
- treat AI-generated notes as user-authored records.

## Validator conformance

A validator SHOULD perform both schema checks and semantic checks.

Schema checks include:

- required fields;
- enums;
- string formats;
- disallowing unknown top-level fields.

Semantic checks include:

- referential integrity;
- hash verification;
- status vocabulary enforcement;
- derived metric rule linkage;
- extension namespace warnings;
- privacy/profile consistency.

The included `reference/validate.mjs` is intentionally small. Production validators SHOULD add full JSON Schema validation.

## Compatibility claims

An app may claim `BehaviorLog Core Compatible` when it can export a Level 1 bundle and pass the official core conformance tests.

Profile compatibility claims SHOULD name the profile:

```text
BehaviorLog Intervention Profile Compatible
BehaviorLog Context Profile Compatible
BehaviorLog Analytics Profile Compatible
```

Compatibility claims SHOULD include the supported schema version.
