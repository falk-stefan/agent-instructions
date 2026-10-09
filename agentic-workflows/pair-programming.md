# Workflow: Pair Programming

## Purpose

Implement a task in chunks, with every chunk reviewed by a second agent before it counts as done. The lead keeps the
work moving and only involves the user where it matters.

## How To Invoke

> "Implement #123 by following `agent-instructions/agentic-workflows/pair-programming.md`."

The task must be named (issue, link or description). Without one, ask the user first.

## Participants

| Role                                          | Lifetime                 | Job here                                         |
|-----------------------------------------------|--------------------------|--------------------------------------------------|
| Lead                                          | The dispatching agent    | Plans chunks, relays, answers, collects questions |
| [Code Implementer](../roles/code-implementer.md) | Hot for the whole task | Implements each chunk                            |
| [Code Reviewer](../roles/code-reviewer.md)    | Hot for the whole task   | Reviews each chunk, checks its findings got fixed |

## Procedure

### 1. Plan

- Split the task into chunks that can each be reviewed on their own
- Brief the Implementer with the task, the chunk plan and the relevant files

### 2. Loop Per Chunk

1. The Implementer implements the chunk
2. The Reviewer reviews the chunk's changes
3. The lead sends the findings back to the Implementer
4. Repeat steps 2–3 for at most **2 review rounds**. Findings still open after that go to the user.

### 3. Questions From The Implementer

- **Answer is in the ticket or the conversation:** the lead answers it
- **Blocking (requirement gap):** ask the user right away
- **Minor:** park it, continue with the parts it does not affect, and present all parked questions in one batch at the
  end of the chunk

### 4. Disagreements

The Implementer may reject a finding, with a reason.

- **Objective (correctness, a broken convention, a failing test):** the lead decides
- **Anything else:** goes to the user

## Guardrails

- Neither agent commits or pushes without the user's approval
- Retire and replace an agent per [hot and cold agents](../roles/user-facing-agent.md#hot-and-cold-agents)
- Reports follow [reports](../roles/user-facing-agent.md#reports)

## Output

After each chunk, and at the end:

```
## Chunk <n>: <title>

<what was done, a few bullets>

**Review:** <n> round(s), <n> findings fixed

**Parked questions:**
- <question>

**Unresolved:**
- <finding> — Implementer: <reason> | Reviewer: <reason>
```

Omit empty sections.
