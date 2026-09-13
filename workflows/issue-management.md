# Issue Management — Product to GitHub Issues Pipeline

## Purpose

Transform a high-level product idea into a structured GitHub Issues hierarchy that:
- Provides stable issue references for commits and PRs
- Creates searchable context usable by humans and agents across separate sessions

GitHub issues are the single source of truth.

---

## Repo Scope

Issue numbers are per-repo. Create an issue in the repo whose code it concerns.

A bare `#N` in a commit message, PR body, or closing keyword resolves within that same repo. If it
lands in the wrong repo, it either resolves to nothing or — worse — to a different, unrelated issue
that happens to share the number. Referencing an issue from another repo anywhere requires the
qualified form: `<owner>/<repo>#N`.

---

## Hierarchy

GitHub is flat by nature. We impose structure via **labels** and **sub-issues**. These serve different purposes and are both used:
- **Sub-issues** — model the parent-child relationship between Epics and Tasks in the GitHub UI
- **Labels** — enable filtering by type (`gh issue list --label task`); they are not redundant with sub-issues

| Level | Label | Description |
|-------|-------|-------------|
| Epic  | `epic` | High-level initiative. Tasks are attached as sub-issues. |
| Task  | `task` | Isolated unit of work. Always added as a sub-issue of its parent Epic. |
| Bug   | `bug`  | A defect. Added as a sub-issue of an Epic if part of a larger effort. |

A Task is worth creating when it can be worked and reviewed on its own. Work that only makes sense
merged with its neighbour is an acceptance criterion on that neighbour, not a Task of its own — an
Epic with fifteen Tasks is usually a sizing failure, not a large Epic.

Type labels: `feature`, `enhancement`, `blocked`.

**Titles** are imperative and name the outcome: "Debounce the config write", not "Config writes
are too frequent" or "dsp: debouncing". No component prefix — that is what labels are for.

**Component labels** name the part of the system an issue touches. Use them instead of prefixing
the title with a component name, and compose them freely with type labels — an issue can be both
`task` and a component. Run `gh label list` to see the components in use; create a new one only
when a genuinely new area of the system appears.

Linking convention: every Task/Bug is attached to its parent Epic via GitHub's native sub-issues API (not just a body reference). The body may still include a `## Part of` section for human readability.

**Sub-issue completion** is driven by issue state: closing an issue marks it complete in the Epic's progress bar automatically. No manual checklist updates needed.

### Epics

An Epic is a milestone, a feature, or any body of work large enough to hold Tasks and with a
recognisable end.

Every milestone in the project's spec gets an Epic, named for it. Not every Epic is a milestone.

An Epic closes on a **stated condition**, not when its last task closes. For a milestone Epic that
condition is the milestone's acceptance criterion, quoted **verbatim** in the body so it is not
restated from memory later. Every other Epic states its own closing condition in the same place.

Do not open a milestone Epic before its milestone is next — a later milestone's shape depends on
what the current one produces. That restraint applies to milestones only: open the others when the
idea arrives, and leave them childless until there is work to attach.

---

## Step-by-Step

Follow these steps in order whenever asked to create issues.

1. **Clarify** — If the request is fundamentally unclear, ask before proceeding.
2. **Check for duplicates** — Search the repo before creating anything: `gh issue list --search "<keywords>" --state all`. Include closed issues; a bug filed twice is usually one that was closed once already. If an Epic covering the work exists, create Tasks under it instead of a new Epic.
3. **Investigate** — Read the relevant code. Collect what the Context section needs: files involved, decisions already settled, acceptance criteria.
4. **Draft** — Draft each issue body from the templates below, using what step 3 turned up. Only the body is drafted; the title and labels are passed via CLI.
5. **Review with user** — Show the draft and ask for approval before creating anything.
6. **Create** — Run the CLI commands. Add labels, add Tasks as sub-issues of their Epic, and add all issues to the project board. `gh issue create --label` fails if the label does not exist in the repo yet, so check `gh label list` first and create what is missing: `gh label create <name> --description "<what it marks>"`.
7. **Create branch** — After creating the Epic, ask the user whether to create its branch now (see below). Skip if they want to defer it.

---

## Branches

| For | Name | Forks from | Merges into |
|-----|------|------------|-------------|
| Epic | `epic-<number>/<short-name>` | default branch | default branch |
| Task | `task-<number>/<short-name>` | its Epic's branch | its Epic's branch |
| Bug | `bug-<number>/<short-name>` | its Epic's branch, or the default branch if standalone | wherever it forked from |

`<number>` is the issue number and `<short-name>` is a few kebab-case words from the title. An
Epic's branch merges to the default branch when the Epic's closing condition is met, not when its
last Task lands.

---

## Writing Style

Follow [writing-style.md](../conventions/writing-style.md). One rule specific to issues: no
implementation detail in Epics.

---

## Templates

### Epic

GitHub tracks sub-issue progress natively — no `## Tasks` checklist needed in the body.

```markdown
## Description
<What is being built or changed — one short paragraph or bullets.>

## Motivation
<Why this matters — one short paragraph or bullets.>

## Functional Requirements
- <User/product-facing statement of what the feature must do.>
- <Another requirement — concrete but not implementation-specific.>

## Closing Condition
<The condition under which this Epic closes. For a milestone Epic, the milestone's acceptance
criterion quoted verbatim.>
```

Labels: `epic`, `feature` (or `enhancement` / `bug`)

---

### Task

```markdown
## Description
<What specifically needs to be done — concrete and scoped.>

## Part of
#<epic-issue-number> — <Epic title>

## Context
- **Files involved:** `<path/to/file>`, `<path/to/other>`, ...
- **Decisions already made:** <any constraints or choices that are settled>
- **Acceptance criteria:**
    - <What must be true for this to be done. Observable, not a restatement of the description.>
    - <Another criterion.>
```

Labels: `task`

---

### Bug

```markdown
## Description
<What is broken.>

## Steps to Reproduce
1. ...
2. ...

## Expected vs Actual
- **Expected:** ...
- **Actual:** ...

## Part of
#<epic-issue-number> — <Epic title>  _(omit if standalone)_

## Context
- **Files involved:** `<path/to/file>`, `<path/to/other>`, ...
- **Acceptance criteria:**
    - <What must be true for this to be fixed — usually that the reproduction no longer reproduces.>
```

Labels: `bug`

---

## GitHub CLI Commands

```bash
# Create an issue — pass the body on stdin so Markdown survives unmangled
gh issue create --title "<title>" --label epic,feature --body-file - <<'EOF'
## Description
...
EOF

# Search this repo for existing issues, open and closed
# (gh search issues, by contrast, searches all of GitHub unless given --repo/--owner)
gh issue list --search "<keywords>" --state all
gh issue list --label epic --search "<keywords>" --state all

# Open issues assigned to yourself, in this repo
gh issue list --assignee @me --state open

# List issues with a specific label
gh issue list --label task

# List the labels that exist in this repo, and create a missing one
# (creating an issue with a label the repo does not have fails)
gh label list
gh label create <name> --description "<what it marks>"

# Add a label to an existing issue
gh issue edit <number> --add-label task

# Viewing issue details
gh issue view <number> --json title,state,body
```

### Sub-issues

```bash
# Add a Task as a sub-issue of its parent Epic
# (requires the integer database ID from the REST API — gh issue view --json id returns a node ID
# string, not an integer)
CHILD_ID=$(gh api repos/<owner>/<repo>/issues/<child-number> --jq .id)
gh api repos/<owner>/<repo>/issues/<parent-number>/sub_issues \
  --method POST -F sub_issue_id="$CHILD_ID"
```

When wiring up multiple issues, run each command individually — one per issue. Do not batch them
into a loop over a list of numbers; a single failure is then silent.

### Project board

Add every created issue to the project board:

```bash
gh project item-add <project-number> --owner <owner> \
  --url "$(gh issue view <number> --repo <owner>/<repo> --json url -q .url)"
```

### Transferring an issue between repos

Use this to fix an issue created in the wrong repo (see [Repo Scope](#repo-scope)).

The issue gets a new number in the target repo — update any commit or PR that already referenced
the old `#N`. Labels must already exist in the target repo or they are silently dropped, and an
existing sub-issue link follows the issue and must be re-pointed:

```bash
gh issue transfer <number> <owner>/<target-repo> --repo <owner>/<source-repo>

# detach from the old parent, then attach to the new one
CHILD_ID=$(gh api repos/<owner>/<target-repo>/issues/<n> --jq .id)
gh api repos/<owner>/<source-repo>/issues/<old-parent>/sub_issue --method DELETE -F sub_issue_id="$CHILD_ID"
gh api repos/<owner>/<target-repo>/issues/<new-parent>/sub_issues --method POST -F sub_issue_id="$CHILD_ID"
```

---

## Closing Issues via PR

Add `Closes #N` (or `Fixes #N`) to a PR description to auto-close the issue when the PR merges. This also marks the sub-issue complete in the Epic's progress bar:

```markdown
## Summary
- <What the PR does>

Closes #<number>
```

Multiple issues can be closed in one PR: `Closes #<number>, closes #<other-number>`.

Same [repo scope](#repo-scope) rule applies: across repos, use `Closes <owner>/<repo>#N`.

---

## Work Spanning Multiple Repositories

- **A milestone that spans repos gets one Epic per repo it touches**, cross-linked in the bodies,
  because GitHub's sub-issues do not span repositories cleanly. The Epic in the repo that owns the
  spec holds the acceptance criterion verbatim; each other Epic states its own narrower criterion
  and points back.
- **Cross-link related issues with the fully qualified form** (`<owner>/<repo>#<number>`), since a
  bare `#<number>` resolves to the current repo.
- **All repositories share one project board.**

---

## Related

- [commit-messages.md](../conventions/commit-messages.md) — referencing issues from commits
