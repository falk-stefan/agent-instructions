# Workflow: PM × PA Feature Score

## Purpose

Get an independent, debated RICE score for a single feature, by pitting the
[Product Manager](../roles/product-manager.md) (speed, market, customer impact) against the
[Product Architect](../roles/product-architect.md) (effort, technical risk) — instead of one agent
silently balancing both concerns itself.

## How to invoke

> "Score issue #57 by following `agent-instructions/agentic-workflows/pm-pa-feature-score.md`."

The feature must be named explicitly — by issue number, link, title, or description. A bare "give
this a RICE score" with nothing to score is not enough scope to dispatch on; the relay asks the
user to name the feature first (see [Scope](#procedure) below).

## Participants

| Role | Instructions | Job here |
|------|-------------|----------|
| Product Manager (PM) | `roles/product-manager.md` | Score the feature's customer/market impact |
| Product Architect (PA) | `roles/product-architect.md` | Score the feature's effort/technical risk |
| Relay | You, the dispatching agent | Coordinate, enforce guardrails, compute the RICE score from each side's blind inputs, digest any unresolved flag — never decide the final score itself |

## Source

This workflow is agnostic about where the feature is described — an existing GitHub issue, a link,
or a plain description the user gives inline. The relay looks up whatever detail already exists;
if the feature has no issue yet, PM and PA work from the description given.

## Procedure

1. **Scope.** The user must name the feature to score (issue number, link, title, or description)
   — this workflow never invents scope itself. If the request didn't name one, stop and ask the
   user to name it before dispatching anything.
2. **Dispatch independently.**
   - **Gather calibration history first.** Look up prior Epics carrying a non-empty `## RICE`
     section, sorted by creation date (most recent first), and take up to 5. For
     each, extract only the two-sentence description and the *relevant role's own* component
     scores — never the other role's fields, never the combined RICE value. Build one table per
     role:

     PM history: `| Feature | Description | R | I | C |`

     PA history: `| Feature | Description | E | C |`

   - Spawn PM and PA as two separate agents. Each gets its role file, the feature named in step 1,
     its own calibration-history table, and a neutral prompt to **score, not rank**, it on its own
     two fields per the [Position Template](#position-template) — PM scores Reach and Impact, PA
     scores Effort.
   Neither is told the other's fields, that a combined score will be computed, or how it will be
   combined. This is deliberate: once an agent knows a formula exists, it can shade its own numbers
   to steer the outcome — scoring blind, on a narrow assigned dimension, removes that incentive, it
   still can't remove the directional bias built into the role itself (a PM is still a PM), only
   the temptation to game a known formula. Both act **read-only** — they may read/query GitHub, the
   codebase, and the web, and keep their own working notes, but must not edit, comment, label, or
   otherwise mutate anything without explicit instruction. Neither sees the other's answer yet.
3. **Collect scores.** Each agent researches the feature — capped, not exhaustive: a couple of
   lookups is enough to ground a claim, this is not a full audit — and returns a 1–3 score with a
   one-line justification and a cited source for each of its two fields, per the
   [Position Template](#position-template).
4. **Compute.** The relay computes:

   ```
   RICE = (Reach × Impact × Confidence_PM) / Effort
   ```

   `Confidence_PA` is recorded separately and never folded into the formula — it is a flag, not a
   multiplier. This asymmetry is intentional: Effort is the more auditable estimate, checkable
   against actual files/modules, while Reach and Impact are inherently speculative market
   judgments — so only the market-side confidence discounts the score.
5. **Debate, where warranted.** Debate is reserved for cases where a score itself is suspect:
   - `Confidence_PM` or `Confidence_PA` is Low (1) — the underlying read is shaky and worth
     surfacing before it's treated as settled.
   - A component score looks under-sourced or miscalibrated against the guardrails below.
   For a triggered case, the relay shares the specific score, its source, and one line of rationale
   with the *other* side, and asks whether it changes their own score — e.g. "PA has Low confidence
   on Effort because of open question Y — does that change your Reach/Impact read, PM?". Repeat for
   at most **3 rounds**. Stop earlier if a round produces no score change on either side. Recompute
   RICE after any score change.
6. **Guardrails, enforced by the relay throughout:**
   - No implementation/code discussion — both roles already reject that; redirect if it appears.
   - Every claim needs a cited source — an issue, a customer signal, a named competitor
     feature/page, or specific files/modules. Send back any claim that lacks one.
   - Research is capped — a couple of lookups is enough; redirect either agent if it starts a broad
     audit instead of grounding a claim.
   - Neither side is told the other's score, the RICE formula, or that scores will be combined,
     until a debate round explicitly shares one flagged component (step 5) — and even then, share
     only that specific score.
   - No restating an argument already made — if nothing new surfaces in a round, close the debate.
   - Keep each position compact — a short paragraph or a few bullets, not an essay. Length is not
     an argument.
   - Hard cap of 3 rounds, no exceptions.
7. **Resolve:**
   - **No unresolved Low-confidence flags** → report the computed RICE score as the recommendation,
     with the full component breakdown.
   - **A Low-confidence flag survives 3 rounds of debate** → this is the closest thing to "no
     consensus": the underlying data genuinely doesn't decide it, or a real requirement gap
     couldn't be closed by argument alone. Report the computed score as-is, but mark it **flagged**
     with one line naming what's unresolved (e.g. "Low PA confidence — open scope question on Y
     unresolved after debate"). Present the score plus the flag and let the user decide whether to
     schedule it now or de-risk it first.
8. **Track.** Once the user confirms the result, the relay writes it back to the Epic: add or
   update its `## RICE` section (per
   [issue-management.md](../workflows/issue-management.md#epics)) with this workflow's output, and
   set the Epic's Impact, Effort, and RICE fields on the project board to match, if the board has
   them. When a field is a single-select rather than a number, map the 1–3 scale onto its options
   (e.g. 1=Low, 2=Medium, 3=High).

## Position Template

Every claim must cite a concrete source — no assertions from intuition. Scores are 1–3 (1 = low,
3 = high), except Effort, which inverts the usual read to match S/M/L (1 = small, 3 = large). Each
agent scores its own fields only — never the other side's — and does not know how the scores will
be combined.

**PM scores Reach and Impact:**
- Reach (1–3): `<score>` — `<how many users/customers/events this touches in a given time period —
  measured from usage data or a comparable named estimate, not a future projection>` — source:
  `<usage data, customer signal, market research, or another named source>`
- Impact (1–3): `<score>` — `<how much this moves the needle, per person/event reached, toward this
  feature's actual goal — competitive parity, retention, or acquisition are all valid goals, but
  name which one and, if it's acquisition, the concrete mechanism (virality, referral,
  shareability) driving it>` — source: `<issue / named competitor feature, or "none found">`
- Confidence (1–3): `<score>` — `<how solid the Reach/Impact read is>` — one-line why

**PA scores Effort:**
- Effort (1–3): `<score>` — `<what changes, time required>` — source: `<files/modules touched>`
- Confidence (1–3): `<score>` — `<how solid the Effort/scope read is — name any open question or unresolved dependency here>` — one-line why

## Output format

```
## RICE: <score>

| Reach | Impact | Conf(PM) | Effort | Conf(PA) | RICE |
|-------|--------|----------|--------|----------|------|
| <1-3> | <1-3>  | <1-3>    | <1-3>  | <1-3>    | <computed> |

**PM case:**
- Reach: <justification> — source: <source>
- Impact: <justification> — source: <source>

**PA case:**
- Effort: <justification> — source: <source>

**Flagged:** <what's unresolved>
<omit this section if nothing is flagged>

**Debate:** <n> round(s) run | no debate needed
<if rounds ran: one line per round on what changed, or "no score changed — stopped early">
```

## Scoring more than one feature

Run this workflow once per feature and sort the resulting RICE scores yourself — comparing already-
computed scores is arithmetic, not something that needs a second dispatch of PM and PA.
