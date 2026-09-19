# Agent Instructions

A small, portable library of instructions for coding agents (Claude Code and similar). Drop it
into any project and point the agent at `index.md`.

## Why

A single giant instructions file wastes context and gets stale. This repo splits instructions into
small, single-purpose files, grouped by topic, loaded only when relevant. `index.md` is the
dispatch table: it tells the agent which file to read for which task, so nothing gets loaded
unless the task at hand actually needs it.

## Structure

- `index.md` — the lookup table. Start here.
- `conventions/` — writing and commit conventions.
- `coding-style/` — language- and framework-specific style rules.
- `workflows/` — multi-step procedures (issue management, git worktrees, ...).
- `roles/` — role definitions for agents acting as a specific persona
- `agentic-workflows/` — procedures that coordinate multiple agents/roles against each other

## Using it in a project

Clone this repo as a sibling checkout inside the project's workspace — not a git submodule.
Submodules add friction (detached HEAD, stale pointer commits) without a real benefit here, since
this repo evolves independently of the projects that consume it.

Point the project's own agent instructions (e.g. its `CLAUDE.md`) at `agent-instructions/index.md`
so agents know to check it.

## Principles

- **Lazy loading.** An agent should read only the files a task needs, never the whole repo.
- **Rules, not essays.** State a rule directly. Skip the background on why it's true or when it
  might not apply — that judgment belongs to whoever is reading it in context.
- **No project specifics.** This repo stays generic on purpose, so it can sit unchanged across
  unrelated projects. Project- or product-specific instructions belong in that project's own repo.
- **Human review required.** Agents read these files freely but don't edit them without the human
  in the loop confirming the change first.
