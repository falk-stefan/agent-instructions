# Commit Messages

Format:

```
<type>: <Subject>

<body>

Refs: #12, #34
```

- `<type>` is one of `feat`, `fix`, `chore`, `docs`, `test`, `refactor`, `ci`.
- `<Subject>` is a short description of the change in the imperative.
- The body is optional. It says why, if the subject does not.
- The last paragraph is the `Refs:` trailer. It lists all relevant issues, separated by commas. Use
  `Refs: none` when no issue applies. Issue numbers are per-repo — see
  [issue-management.md](../planning/github-issue-management.md#repo-scope). Use `<owner>/<repo>#N` when
  the issue lives in a different repo than the commit.
- Never credit an agent, tool, model, or its vendor. No `Co-Authored-By` trailer for one, no
  "generated with" footer, no mention anywhere in the message. The commit is authored solely by
  the human who ran the work. The same applies to PR titles and descriptions.

`Refs:` never closes an issue. To close an issue when a PR merges, see
[issue-management.md](../planning/github-issue-management.md#closing-issues-via-pr) — the `Closes #N`
keyword belongs in the PR description, not in the commit message.
