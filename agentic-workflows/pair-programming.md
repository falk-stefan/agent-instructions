# Workflow: Pair Programming

## Purpose

Implement a task, then review it in rounds until no blockers are left. The lead keeps the work moving and only involves
the user where it matters.

All agents use the levels and scope tags in [review severity](../coding/review-severity.md).

## How To Invoke

> "Implement #123 by following `agent-instructions/agentic-workflows/pair-programming.md`."

The task must be named (issue, link or description). Without one, ask the user first.

## Participants

| Role                                             | Lifetime               | Job here                                                     |
|--------------------------------------------------|------------------------|--------------------------------------------------------------|
| Lead                                             | The dispatching agent  | Plans, sets the size budget, relays, answers, decides rounds |
| [Code Implementer](../roles/code-implementer.md) | Hot for the whole task | Implements the task and fixes findings                       |
| [Code Reviewer](../roles/code-reviewer.md)       | Fresh for each round   | Reviews the full change                                      |

## Procedure

### 1. Plan

- Split the task into commits that can each be reviewed on their own, per [commits](../coding/git/commits.md)
- Set a **size budget** for the change, including tests. Use the ticket as a guide. Default: well under 500 lines
- Brief the Implementer with the role file first, then the task, the commit plan, the size budget and the relevant
  files

### 2. Implement

The Implementer implements the full task. The review starts when the full task is done, not after each commit.

### 3. Review Rounds

1. A fresh Reviewer reviews the full change. Its brief: the role file first, then the task, the PR link or worktree,
   and the size budget. No earlier findings and no focus points
2. The lead checks `git diff --shortstat` against the size budget
3. The lead sends the findings to the Implementer:
    - **Round 1:** in-scope blockers, and Worth-fixing findings that are cheap
    - **From round 2:** in-scope blockers only. Other findings go to the final report
    - **Out of scope:** never sent. They go to the user as questions
4. The Implementer fixes the findings and reports back
5. Start a new round only if the last round had in-scope blockers

### 4. Round Limits

- **Target:** 2–3 rounds
- **Up to 5 rounds** are fine when the number of blockers goes down each round
- **At 5 rounds:** stop. Give the open findings to the user

### 5. Stop Early And Consult The User

Stop the loop and ask the user when one of these occurs:

- the number of blockers does not go down, or fixes cause new blockers
- the same area causes blockers again and again
- a finding needs a new subsystem, migration, API field, setting or UI path
- the diff goes past the size budget
- the Implementer and the Reviewer disagree on the same point twice

These are signs of a design or scope problem, not of a missing fix. Tell the user:

- the pattern you see
- the likely cause (design or scope)
- the options, with your recommendation

### 6. Questions From The Implementer

- **Answer is in the ticket or the conversation:** the lead answers it
- **Blocking (requirement gap):** ask the user right away
- **Minor:** park it, continue with the parts it does not affect, and present all parked questions in one batch at the
  end

### 7. Disagreements

Per [review severity](../coding/review-severity.md#disagreement).

## Guardrails

- Neither agent commits or pushes without the user's approval
- Never call a change ready unless a Reviewer saw its latest code change. Trivial changes after the last review do
  not need a new round, per [pull requests](../coding/pull-requests.md)
- Retire and replace an agent per [hot and cold agents](../roles/user-facing-agent.md#hot-and-cold-agents)
- Reports follow [reports](../roles/user-facing-agent.md#reports)

## Output

At the end:

```
## <task>

<what was done, a few bullets>

**Rounds:**
- Round <n>: <n> Critical, <n> Important, <n> Worth fixing, <n> Optional — diff +<x>/−<y>

**Scope questions:**
- <out-of-scope finding>

**Parked questions:**
- <question>

**Not fixed (Worth fixing / Optional):**
- <finding>

**Unresolved:**
- <finding> — Implementer: <reason> | Reviewer: <reason>
```

Omit empty sections.
