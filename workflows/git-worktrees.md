# Git Worktrees

Always ask the user first if creating a local branch is enough or if a worktree should be used!

## Rule

If worktrees are to be used: One worktree per branch. Derive the path from the branch name — never guess, 
never coordinate with other agents.

Branch names follow [issue-management.md](../workflows/issue-management.md#branches):
`epic-<number>/<short-name>`, `task-<number>/<short-name>`, `bug-<number>/<short-name>`.

Path:

```
../worktrees/<repo>/<branch-name>
```

Example: `epic-75/search-filters` in `tourah` lives at
`tourah-workspace/worktrees/tourah/epic-75/search-filters`.

## Steps

1. Work out the branch name first.
2. Check if the worktree exists: `git -C <repo> worktree list`. If it does, use it.
3. If not, create it:

   ```bash
   git -C <repo> worktree add ../worktrees/<repo>/<branch-name> <branch-name>

   # branch doesn't exist yet:
   git -C <repo> worktree add -b <branch-name> ../worktrees/<repo>/<branch-name> <parent-branch>
   ```
4. Do all work for that branch there. Never switch branches inside a worktree — make a new one instead.
5. After the PR merges, clean up:

   ```bash
   git -C <repo> worktree remove ../worktrees/<repo>/<branch-name>
   git -C <repo> worktree prune
   ```
