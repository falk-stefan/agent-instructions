# Workspace Context

Every project repo's `CLAUDE.md` opens with an instruction to find and read the workspace-level
`CLAUDE.md` before doing anything else, and to stop if it can't be found.

## Why

A repo is often opened on its own — a fresh IDE window, a git worktree, a standalone clone.
Without this check, an agent working there never learns the workspace exists: sibling repos,
workspace-wide conventions, this index.

## The instruction

Add this as the first content in a project repo's `CLAUDE.md`, right after the title:

> Before anything else, find the workspace-level `CLAUDE.md`: walk up parent directories from this
> file until one contains an `agent-instructions/` directory, then read its `CLAUDE.md`. Stop and
> tell the user the workspace context is missing if none is found up to the filesystem root.

Search upward instead of assuming a fixed relative path. A plain clone sits one level below the
workspace root, but a [git worktree](../workflows/git-worktrees.md) sits several levels deeper —
a fixed `../CLAUDE.md` would look in the wrong place.
