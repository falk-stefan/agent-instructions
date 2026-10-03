# Agent Instructions

A portable library of instructions for coding agents (Claude Code and similar). 

## Why

A single giant instructions file wastes context and gets stale. This repo splits instructions into
small, single-purpose files, grouped by topic, loaded only when relevant. `index.md` is the
dispatch table: it tells the agent which file to read for which task, so nothing gets loaded
unless the task at hand actually needs it.

## How To Use 

- Clone this repository into `.claude/skills/agent-instructions`
- Create a hook for claude to load the skill immediately

In `.claude/settings.json` or `.claude/settings.local.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo 'CRITICAL: Load the agent-instructions skill before anything else!'"
          }
        ]
      }
    ]
  }
}
```

## Principles

- **Lazy loading.** An agent should read only the files a task needs, never the whole repo.
- **Rules, not essays.** State a rule directly. Skip the background on why it's true or when it
  might not apply — that judgment belongs to whoever is reading it in context.
- **No project specifics.** This repo stays generic on purpose, so it can sit unchanged across
  unrelated projects. Project- or product-specific instructions belong in that project's own repo.
- **Human review required.** Agents read these files freely but don't edit them without the human
  in the loop confirming the change first.
