
# BehaviorLog Bundle

**BehaviorLog Bundle** is an open, versioned export format for recurring behavior, adherence, reminder, and context data. It is designed for migration, pattern discovery, and AI-readable longitudinal self-monitoring.

The core object is not a generic task. The core object is **adherence over time**: a behavior definition, a schedule, generated occurrences, explicit completion decisions, optional reminders, optional context, and declared metrics.

This repository is a draft starter specification for `behaviorlog.bundle` `0.1.0-draft`.

## Design goals

1. **Portable adherence data.** Users should be able to carry their behavior history between apps and agentic tools.
2. **Agent-readable semantics.** AI assistants should not need to guess whether an unmarked occurrence was failed, skipped, cancelled, or simply unresolved.
3. **Strict core, modular surface.** The required core is small. Reminders, context, reviews, analytics, research, and clinical mappings are optional profiles.
4. **Machine validation first.** Files are normalized, schema-described, hash-checkable, and usable offline.
5. **Human reviewability.** Records are JSONL, examples are small, and every bundle contains compact agent instructions.

## What a bundle looks like

A bundle may be a directory ending in `.behaviorlog/` or a `.behaviorlog.zip` archive with the same internal layout.

```text
example.behaviorlog/
  manifest.json
  schema.json
  README.md
  AGENTS.md
  data/
    behaviors.jsonl
    schedules.jsonl
    occurrences.jsonl
    status_events.jsonl
    notes.jsonl                 # optional
    interventions.jsonl         # optional profile
    context_snapshots.jsonl     # optional profile
    reviews.jsonl               # optional profile
    derived_metrics.jsonl       # optional profile
  csv/                          # optional human/migration views
  raw/                          # optional app-native exports
```

## Core vocabulary

| Term | Meaning |
|---|---|
| Behavior | A recurring action, avoidance, practice, routine, or adherence target. |
| Schedule | A rule that generates expected occurrences. |
| Occurrence | One scheduled instance of a behavior. |
| Status event | An explicit or declared record of whether an occurrence is unresolved, completed, or not completed. |
| Intervention | A reminder, prompt, suppression, nudge, or notification decision. Optional profile. |
| Context snapshot | Coarse contextual data linked to an occurrence, intervention, or status event. Optional profile. |

The required status vocabulary is intentionally small:

```text
unresolved
completed
not_completed
```

`unresolved` is not `not_completed`. `not_completed` means an explicit user decision or a manifest-declared high-confidence source says the behavior was not completed. The core standard does not store `missed` as a status.

## Why not CSV alone?

CSV is useful as a migration view, but it cannot safely preserve nested recurrence rules, status provenance, context precision, reminder histories, and metric denominator rules. BehaviorLog uses JSONL as the canonical event format and permits CSV as a derived view.

## Profiles

The core profile is enough to migrate and analyze basic adherence.

Optional profiles add scoped data:

- **Intervention Profile:** reminders, prompts, notification delivery, snoozes, suppressions, and burden.
- **Context Profile:** calendar availability, coarse place labels, activity state, mood/energy, and other explicitly exported context.
- **Review Profile:** weekly/monthly reflections and adjustments.
- **Analytics Profile:** precomputed metrics with manifest-declared formulas.
- **Research Profile:** experiment metadata, randomization, micro-randomized trials, and advanced provenance.
- **Mapping Profiles:** CSV, xAPI, FHIR/Open mHealth, iCalendar, PROV, RO-Crate, ActivityStreams, and app-specific importers.

## Quick start

Validate the included examples with the zero-dependency reference checker:

```bash
node reference/validate.mjs examples/basic.behaviorlog
node reference/validate.mjs examples/intervention-context.behaviorlog
node reference/validate.mjs examples/edge-cases.behaviorlog
```

The reference checker performs bundle-level and semantic checks. JSON Schema validation is still recommended for production writers and readers.

## Repository map

```text
README.md                 Overview and adoption guide
SPEC.md                   Normative core specification
RATIONALE.md              Design philosophy, tradeoffs, and research anchors
PRIVACY.md                Redaction, sensitivity, and consent guidance
CONFORMANCE.md            Reader/writer classes and validation rules
MAPPING.md                External standard and app mapping notes
GOVERNANCE.md             Contribution, RFC, and compatibility process
schema/                   JSON Schema contract
examples/                 Synthetic bundles
reference/validate.mjs    Minimal semantic validator
questions/                Canonical questions for agent evaluation
```

## Minimal producer checklist

A conforming core writer MUST:

1. Emit all required files.
2. Use the core status vocabulary exactly.
3. Preserve local dates and IANA time zones.
4. Store status changes as events, not destructive overwrites.
5. Put non-standard fields only under `extensions`.
6. Declare derived metric formulas in `manifest.json`.
7. Include hashes for required files.
8. Include `AGENTS.md` inside the bundle.

## Status

This is a draft. The intended path is:

1. Stabilize the core event model.
2. Build readers and validators.
3. Add mappings and examples through profile RFCs.
4. Invite app vendors, quantified-self developers, and researchers to test import/export compatibility.

## License

MIT. See `LICENSE`.
