
# Governance

BehaviorLog Bundle is intended to be a neutral community standard.

## Stewardship

Early development may be led by the initiating project, but the format should be governed as an app-independent standard. Cadence or any other app should be treated as one implementation, not the owner of the data model.

## License

The repository uses the MIT License for docs, schemas, examples, and reference code unless a future governance decision separates documentation and code licenses.

## Versioning

The standard uses semantic versioning after the first stable release.

Before `1.0.0`, breaking changes may occur, but they MUST be documented in `CHANGELOG.md`.

## RFC process

New core fields, profiles, and mappings SHOULD be proposed through an RFC.

An RFC SHOULD include:

1. Motivation.
2. User benefit.
3. Agent/readability impact.
4. Schema changes.
5. Example records.
6. Privacy impact.
7. Migration impact.
8. Conformance tests.
9. Alternatives considered.

## Extension registry

The core standard permits private extension namespaces under `extensions`.

A future registry MAY list public extension namespaces and profile IDs. Until then, extension authors SHOULD use reverse-DNS or URL namespaces.

## Compatibility badges

Compatibility claims should be test-backed.

Suggested badges:

```text
BehaviorLog Core Compatible
BehaviorLog Intervention Profile Compatible
BehaviorLog Context Profile Compatible
BehaviorLog Analytics Profile Compatible
BehaviorLog Research Profile Compatible
```

## Contribution priorities

Near-term contributions should focus on:

1. Core schema review.
2. Validator improvements.
3. More synthetic examples.
4. CSV mapping tests.
5. App importer/exporter pilots.
6. Intervention and context profile hardening.
7. Research-grade mapping proposals.

## Design discipline

The standard should remain adherence-centered. New concepts should be rejected from core when they can be represented as notes, extensions, or profiles.
