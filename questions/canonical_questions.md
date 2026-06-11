
# Canonical Agent Questions

Use these questions to evaluate whether an agent correctly understands a BehaviorLog Bundle.

## Core questions

1. Which behaviors have the highest explicit adherence rate?
2. Which behaviors have the highest unresolved rate?
3. Which occurrences are unresolved and prior to the current local date?
4. Which behaviors are often marked `not_completed` rather than left unresolved?
5. What is the difference between explicit adherence rate and scheduled completion rate in this bundle?
6. Which schedules changed during the export period?
7. Which occurrences were cancelled and excluded from denominators?
8. Which statuses were imported ambiguously and should not be trusted as explicit non-completion?
9. Which behaviors lack useful success definitions?
10. Which records contain extensions that the agent does not understand?

## Intervention Profile questions

1. Which reminders were sent but not followed by completion within the declared response window?
2. Which behaviors generate the most snoozes or dismissals?
3. Did any reminders fail to deliver?
4. Were any reminders suppressed because the occurrence was already resolved?
5. What burden signals should be reported alongside reminder response rate?

## Context Profile questions

1. Which context values are coarse versus precise?
2. Which contexts are associated with better adherence, without claiming causation?
3. Does the bundle contain raw or restricted context?
4. Which context sources require special privacy handling?

## Expected answer discipline

A good answer states:

- the metric denominator;
- the number of unresolved occurrences;
- the local date range and timezone;
- whether optional profiles were present;
- whether source data or derived metrics were used;
- any privacy or ambiguity limitations.
