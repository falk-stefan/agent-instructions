# Review Severity

One scale for every review. The reviewer, the implementer and the lead use the same levels, so all agree on what must
be fixed and what is optional.

## Levels

| Level            | Meaning                                         | Examples                                                                                                                                                           | Effect                                                              |
|------------------|-------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| **Critical**     | Wrong now                                       | Bug, data loss, security hole, broken migration, unintended API break, wrong payment amount, failing test                                                         | Fix now. Blocks the change.                                         |
| **Important**    | Not broken yet, but becomes a problem later     | Race under horizontal scaling, missing error path for realistic input, untested business rule, logic in the wrong layer, a pattern that others will copy         | Fix now. Blocks the change.                                         |
| **Worth fixing** | Clear improvement, cheap, inside scope          | Small duplication, unclear naming, a missed shared helper                                                                                                          | Fix in the same round if cheap. Never starts a new round by itself. |
| **Optional**     | Preference or nit                               | Another style that also works                                                                                                                                      | Report only. Not fixed in the review loop.                          |

Critical and Important are **blockers**.

When unsure between two levels, pick the lower one and say why.

## Scope

Every finding also has a scope tag. Scope is separate from the level.

- **In scope:** the fix stays inside the task (ticket, issue or description).
- **Out of scope:** the fix needs something the task does not ask for, for example a new rule, subsystem, migration,
  API field, setting or UI path.

An out-of-scope finding is a question for the user, never an automatic fix. This is also true for a Critical finding.

A problem in existing code outside the diff is not part of the change. Propose a separate issue for it.

## Finding Format

```
[<Level> | <in scope | out of scope>] <file>:<line> — <problem>
Evidence: <what in the code shows it>
Fix: <smallest fix>, about <n> lines
```

## Disagreement

- The implementer may dispute a finding or its level, with a reason.
- A dispute about the level or about correctness: the lead decides.
- A dispute about scope: the user decides.
