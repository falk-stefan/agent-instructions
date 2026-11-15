# Commits

Humans review a pull request commit by commit. Each commit is one step that a reviewer can read and understand on its
own.

Commit messages follow [commit messages](./commit-messages.md).

## One Commit, One Purpose

- One commit holds one logical change. If the subject needs an "and", split the commit.
- Keep tests in the same commit as the code they test.
- Keep mechanical changes in their own commit: moves, renames, formatting, generated files, bulk import rewrites. Do not
  mix them with logic changes. A reviewer can then skim them.
- Each commit builds and keeps the tests it touches green, where practical.

## Order

Tell the story in the order a reviewer needs it:

1. `refactor:` prepare the existing code (extract, move, rename). No change in behaviour.
2. `feat:` / `fix:` the change itself, with its tests.
3. `docs:` / `chore:` docs, config, generated files that follow from the change.

Skip the steps that do not apply.

## Balance

Not too few, not too many.

- **Too few:** one commit mixes a refactor, the feature and generated files. The reviewer cannot see which lines change
  behaviour.
- **Too many:** commits like "wip", "fix typo", "fix lint", "address review". They are noise.

Rough guide:

| Pull request                         | Commits                              |
|--------------------------------------|--------------------------------------|
| Small, one concern                   | 1                                    |
| Typical                              | 2–5                                  |
| More than about 7 meaningful commits | Ask if it should be more than one PR |

Test each commit: does it make sense alone? If two commits only make sense together, squash them. If one commit does two
unrelated things, split it.

## Fixes

- **Before review:** fold a fix into the commit it belongs to (`git commit --fixup=<sha>`, then
  `GIT_SEQUENCE_EDITOR=: git rebase -i --autosquash <base>`) and push with `--force-with-lease`.
- **After review started:** never rewrite history. Add new commits. Group the fixes of one review round by topic, not
  one commit per finding.
- Never rewrite `main` or a branch that another person works on.
