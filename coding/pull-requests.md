# Pull-Request Instructions

- Create pull-requests as drafts. A pull-request leaves draft only after a reviewer (the user or a reviewer agent)
  checked its latest commit and CI is green. Any new commit needs a new review.
- Add the labels the repo uses. If the repo has a release-notes config (e.g. `.github/release.yml`), add the label
  for the right category.
- Split the work into commits per [commits](./git/commits.md).
- Titles and descriptions follow the no-credit rule in [commit messages](./git/commit-messages.md): never credit an
  agent, tool or model.

## Template _(if no other template was provided)_

If no template was provided, use the following as a default:

```markdown
## Description

<What is being implemented or changed — one short paragraph or bullets.>

## Motivation

<Why this matters — one short paragraph or bullets.>

## Changes

### Before _(optional)_

<The behavior before>

### After

<What changes>

## Tests

<Description of tests. Not an endless list of individual tests but a readable explanation or bullet-list.>

---
<List of issues it closes; Use correct format for GitHub auto-close>
```
