
# Rationale and Philosophy

BehaviorLog Bundle begins from a narrow claim: the durable object in a behavior tracker is not the app, dashboard, streak, or notification surface. It is the user's longitudinal adherence context.

As personal software becomes more agentic, users will move between custom tools, assistants, research interfaces, and app surfaces. A behavior log should therefore be portable as context, not trapped as a proprietary product database.

## Why adherence is the core

Most habit and behavior trackers exist to answer a simple question: did the intended behavior actually happen, and under what conditions does it tend to happen or fail?

That question requires more than a checkbox. It requires:

- a behavior definition;
- a schedule or recurrence model;
- generated occurrences;
- explicit completion or non-completion decisions;
- unresolved states that are not silently counted as failure;
- reminder/intervention exposure when available;
- contextual or qualitative notes when available;
- metric rules with explicit denominators.

The standard is therefore built around adherence rather than generic task management.

## Research anchors

The standard is intentionally compatible with core behavior-change constructs without hard-coding any one branded productivity method.

Relevant anchors include:

- **Self-monitoring, feedback, prompts/cues, and goals** recur across digital behavior-change and mobile health research as important engagement and adherence ingredients.
- **Implementation intentions** support the transformation of intention into action by linking cues to concrete behavior.
- **Habit formation** depends on repetition in stable contexts and varies widely between people and behaviors.
- **Just-in-time adaptive intervention** research distinguishes decision points, intervention options, tailoring variables, proximal outcomes, and decision rules. BehaviorLog keeps those concepts out of the core, but makes room for them through profiles.

The result is a standard that can express simple manual tracking and also support future intervention, context, and research workflows.

## Why a bundle

A single CSV file cannot carry enough semantics for reliable agentic analysis. It loses recurrence definitions, status provenance, privacy labels, denominator rules, optional context, and source mapping decisions.

A bundle lets different consumers read different layers:

- apps can import core JSONL files;
- users can inspect README and CSV views;
- agents can read `AGENTS.md` and the manifest;
- validators can check schema and hashes;
- researchers can use optional context, intervention, and analytics profiles.

This follows the general pattern of manifest-centered data packages while adding behavior-specific semantics.

## Why JSONL as canonical

Behavior logs are event streams. JSONL keeps each record independently parseable and makes append-only histories natural. It also remains readable in a text editor and streamable by tools.

CSV remains useful for migration and spreadsheet inspection, but it is a view, not the source of truth.

## Why `unresolved` is protected

Many trackers collapse silence into failure. That is harmful for analysis.

A prior unresolved occurrence may mean:

- the user forgot to log;
- the app failed to notify;
- the occurrence is still awaiting review;
- the behavior was impossible that day;
- the user has not decided how to classify it;
- the imported app had no equivalent status.

Therefore `unresolved` is a first-class status and MUST NOT be treated as `not_completed`.

## Why no core `missed`

`missed` is often a UI interpretation, not a source fact. It may mean unresolved after the due window, explicitly not completed, skipped, cancelled, late, or impossible.

BehaviorLog makes those distinctions explicit:

- `not_completed` for explicit non-completion;
- `unresolved` for no explicit decision;
- `occurrence_state: cancelled` for excluded occurrences;
- reason codes or profiles for skipped/impossible cases;
- metrics for derived missed-like labels if a manifest rule defines them.

## Why profiles

The standard should be simple enough for small apps and expressive enough for richer systems. Profiles let implementers add scoped capabilities without contaminating the core.

Examples:

- A simple tracker can export only behaviors, schedules, occurrences, and status events.
- A reminder-heavy app can add the Intervention Profile.
- A context-aware app can add coarse calendar/location/activity snapshots.
- A research study can add experiment metadata and richer provenance.
- A clinical app can map to FHIR or Open mHealth without making clinical semantics mandatory for everyone.

## Why agent instructions are inside the bundle

Agents need more than raw data. They need reading order, forbidden inferences, privacy warnings, and metric rules.

Each bundle includes a compact `AGENTS.md` so a receiving assistant knows how to interpret the data before making claims about the user.

## Rejected alternatives

### One large relational export

Rejected because it is harder for agents and simple tools to inspect, stream, and partially import.

### CSV-only

Rejected because it loses nested recurrence, provenance, metric rules, and context precision.

### FHIR-first

Rejected because many behavior logs are not clinical records. FHIR mappings are useful, but clinical semantics should not define the core.

### Full quantified-self schema

Rejected because the core should remain about adherence. Measurements, sensors, goals, projects, focus sessions, and experiments belong in profiles.

### Automatic missed status

Rejected because it silently converts absence of data into a negative outcome.

## Reference influences

BehaviorLog borrows selectively from:

- manifest/data-resource package patterns;
- JSON Schema validation;
- JSONL event streams;
- iCalendar recurrence concepts;
- provenance modeling;
- xAPI actor-verb-object event statements;
- FHIR task/care-plan concepts for optional clinical mappings;
- agent instruction files such as `AGENTS.md`.

It does not copy any of these wholesale. The adherence-specific semantics are the reason this standard exists.
