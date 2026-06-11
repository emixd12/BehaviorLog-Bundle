
# AGENTS.md

This repository defines the BehaviorLog Bundle standard. Treat `SPEC.md` as the normative source for required behavior. Treat `RATIONALE.md`, `PRIVACY.md`, `CONFORMANCE.md`, and `MAPPING.md` as supporting guidance.

## Read order

1. `README.md`
2. `SPEC.md`
3. `CONFORMANCE.md`
4. `PRIVACY.md`
5. `MAPPING.md`
6. `schema/behaviorlog.schema.json`
7. `examples/*`
8. `reference/validate.mjs`

## Core invariants

- Do not add `missed` to the core status vocabulary.
- Do not treat `unresolved` as `not_completed`.
- Do not make `skipped`, `snoozed`, or `cancelled` core statuses. Model them through reasons, occurrence state, or optional profiles.
- Preserve the separation between behaviors, schedules, occurrences, status events, interventions, context, and derived metrics.
- Put unknown or vendor-specific data only under `extensions`.
- Keep the core small; add adjacent concepts through profiles.
- Use app-neutral terms in the standard. App-specific terms belong in mappings.
- Use IANA time zones and local dates for behavior-day analysis.
- Require declared denominator rules for adherence metrics.

## Writing rules

- Use formal but concise language.
- Prefer normative `MUST`, `SHOULD`, and `MAY` in `SPEC.md` and `CONFORMANCE.md`.
- Keep examples small and synthetic.
- Keep agent instructions compact enough to fit into context.
- Do not turn this standard into a medical, productivity, social, or gamification schema by default.

## Validation commands

```bash
node reference/validate.mjs examples/basic.behaviorlog
node reference/validate.mjs examples/intervention-context.behaviorlog
node reference/validate.mjs examples/edge-cases.behaviorlog
```

## Completion criteria for changes

A change is complete only when:

1. The spec and schema agree.
2. Examples still validate.
3. New profile fields are documented in `SPEC.md` or profile docs.
4. New non-core ideas are scoped as profiles, not silently added to core.
5. `CHANGELOG.md` records the change.
