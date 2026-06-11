
# Contributing

BehaviorLog Bundle is draft-stage. Contributions should improve portability, validation, agent interpretability, and privacy.

## Before proposing a change

Read:

1. `SPEC.md`
2. `RATIONALE.md`
3. `CONFORMANCE.md`
4. `PRIVACY.md`

## Good first contributions

- Add invalid example bundles for validator tests.
- Improve schema coverage.
- Add app-specific mapping notes.
- Add conformance fixtures.
- Add profile RFC drafts.
- Improve README diagrams.

## Change requirements

A schema or spec change should include:

- updated documentation;
- updated schema;
- at least one example;
- validator changes if semantic behavior changes;
- changelog entry.

## Scope rule

Do not add adjacent objects to the core unless they are necessary for basic adherence portability.

Examples that usually belong in profiles:

- experiments;
- goals and OKRs;
- projects;
- passive sensing;
- rich measurements;
- gamification;
- clinical medication semantics.
