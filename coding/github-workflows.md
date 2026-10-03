# GitHub Workflows

## Rule

Workflows orchestrate; they do not contain logic. Everything a workflow runs can be run and tested
locally the same way.

## Steps

- A step calls one command: `pnpm run <script>`, a script in the repository, or a single tool
  invocation (`gcloud run deploy ...`, `docker build ...`).
- Keep `run:` blocks to a few lines. A longer block is a script waiting to be extracted.
- Pass inputs to scripts as arguments or environment variables. Scripts do not read the `github.*`
  context.
- Never print secrets or fetched environment files to the log.

## Scripts

- Branching, loops, parsing or version logic belong in a script, not in a `run:` block.
- Scripts that belong to an app live in its `package.json`. Scripts that only serve CI live in
  `.github/scripts/`.
- Write scripts with logic in TypeScript, like the rest of the codebase. Plain shell is fine for a
  short sequence of CLI calls without logic.
- A script documents its usage in a header comment and runs locally without CI-only setup.
