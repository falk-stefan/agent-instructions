# Supervisor: Parallelize Implementation Work

## Gist

The parent agent is a supervisor. It splits well-designed work into independent units, dispatches one
sub-agent per unit and merges their results. It fits work where every unit follows the same, written-down
recipe. It doesn't fit work that still needs design decisions.

## Prepare

- Write the recipe down once: a checklist for every unit, plus one line per unit for what is specific
  to it.
- Land what the units share first, e.g. a helper and one worked example.
- Put the sub-agent rules in one file. Each prompt then only names the rules file, the unit, its line
  and its worktree.

## Dispatch

- Run 1 to 6 sub-agents at once, on Sonnet.
- Each unit gets its own worktree and branch off the parent branch. The supervisor creates both.
- Run units that depend on each other one after the other.

## Sub-agent rules

- Think briefly and read only what a failing test points to.
- Change with scripted search and replace. Then run the unit's own tests and fix what breaks.
- Don't run the linter, the type check or the full test suite. Run every command in the foreground.
- Do only the unit's line and the checklist. If something doesn't fit, leave it and report it.
- Commit on the unit's branch. Don't push or merge. Report in a few lines.

## Merge

- Merge finished branches locally, one at a time. Push only when the user asks.
- Once everything is merged, run the linter, the type check and the full test suite once.
- Hand the user every point a sub-agent left open, and decide them one at a time.

## Example

Aligning the backend API with a new API model: an Epic held the checklist (routes, tags, `PATCH`
returns the entity, access spec) and one row per controller. The supervisor added the access-test
helper first, then ran up to 6 Sonnet agents, one per controller, and merged about 30 branches into the
Epic branch.
