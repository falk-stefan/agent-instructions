# Workflow: Meeting

## Purpose

The purpose of a meeting is to get the right people at the table to discuss an issue from different angles to
arrive at an optimal solution or compromise.

## How to invoke

Meetings are **very expensive**! The meeting workflow may only be invoked if the user has given explicit permission.
All agents must ask the user or the parent agent before invoking a meeting workflow.

> "Run a meeting on `<topic>` following `agent-instructions/agentic-workflows/meeting.md`."

A bare request for a meeting with no topic is not enough to dispatch on — the relay asks the user to name the
topic and goal first (see [Procedure](#procedure) below).

## Participants

Any of the defined roles in `./../roles` are valid invitees. It is the host's responsibility to propose the
minimum set of roles required — with a one-line reason per role — and this proposal is confirmed by the user
before any sub-agent is spawned (see [Procedure](#procedure)).

## Procedure

### 1. Meeting Description

The host drafts a proper meeting description which describes the topic and the goal of the meeting.
A meeting should always focus on one particular topic.
A meeting description must always have at least one clear goal.
Everyone is reminded to work together and solution-oriented.

### 2. Confirm scope, participants, and participation mode

Before spawning anything, the host presents the user with, in one message:

- The meeting description (topic + goal).
- The proposed participant list, with a one-line reason per role.
- A question asking how the user wants to participate:
  1. **Not at all:** only present the outcome.
  2. **Only if necessary:** e.g. a factual gap only the user can fill, or a scope ambiguity surfaces mid-discussion
     that no participant can resolve on their own.
  3. **Actively:** the user is treated like a sub-agent and is part of the discussion.

The host waits for explicit confirmation of description, participants, and mode before proceeding. Given how
expensive a meeting is, this is a hard gate — never inferred or skipped.

### 3. Invites

Every confirmed role is invited in the form of a sub-agent.
Each sub-agent can only take on one role and receives the corresponding description
from `./../roles` as well as the confirmed meeting description.

**Guardrails, in force for every participant, every round:**

- **Read-only by default.** Participants may research, discuss, and keep their own working notes, but must not
  edit, comment, label, commit, or otherwise mutate anything outside the meeting itself without explicit
  instruction from the user or relay.
- **Research is capped.** A couple of lookups is enough to ground a position — this is not a full audit. The
  relay redirects any participant that starts a broad investigation instead of grounding a specific claim.
- These guardrails apply on top of whatever a role's own file already states; they never loosen a role's own
  restrictions, only add a floor for roles that don't otherwise specify.

### 4. Discussion Rounds

The discussion is organized and managed by the agent that initiates the meeting (the relaying agent), across a
hard cap of **3 rounds**. No exceptions. The relay may stop early if a round produces no change in any
participant's position.

Every position or reaction, in every round, must label its confidence as **high**, **medium**, or **low** with a
one-line reason — this is mandatory, as an act of transparency, not merely "whenever applicable."

**Round 1 — Initial positions.** The relay asks every participant to independently prepare a position summary in
a format that suits the discussion, based only on the meeting description — not on any other participant's
input yet. A position summary should be short, concise, and structured, focused only on the essentials, not a
long essay.

**Round 2 — Reaction.** Once all positions are collected, the relay broadcasts one aggregated summary of all
positions to every participant and asks everyone to consider the arguments presented. They may agree with or
challenge arguments accordingly.

**Round 3 — Final.** The relay **informs every participant clearly** that they are entering the last round. In
this round they lay out what they agree and/or disagree with, and why.

**User participation (mode 3):** the user's position is solicited at the same point in each round as the
sub-agents' — the relay waits for it before aggregating and broadcasting that round's summary, and includes it
in the broadcast like any other participant's.

**User participation (mode 2):** the relay pauses and asks the user only when a round surfaces something no
participant can resolve alone (a missing fact, a scope ambiguity) — not for routine disagreements between
roles, which the discussion rounds are meant to work through on their own.

### 5. Presentation

After the last round — or an early exit — the relay prepares an overview of the discussion for the user:

```
# Meeting Notes: <title>

## TL;DR
<Short summary -- most important highlights and the core result>

## Recommendation
<The relay's synthesized recommendation or compromise, grounded in the positions below -- a judgment call the
relay states and justifies, not a vote or an average of positions>

## Unresolved
<Any point participants still disagree on after 3 rounds, one line per side -- omit this section entirely if
nothing is unresolved>

## Positions

### <role>
<Position summary for each role -- short; most important reasoning points, with confidence label>
```

The Recommendation section is the one place the relay exercises judgment on the substance of the discussion —
warranted because the meeting's Purpose is to arrive at a solution, not just catalogue disagreement. The user
remains free to override it: in mode 1 they see it for the first time here, and in modes 2/3 they've already
helped shape it.
