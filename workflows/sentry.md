# Sentry

Query issues and events via `sentry-cli`. Requires an auth token with at least
`project:read` and `event:read` scopes (`sentry-cli info` shows current scopes;
`sentry-cli login` to re-auth).

## Org & projects

- Org slug: `carinthian-technologies`
- Project slugs: `tourah-backend`, `tourah-admin-tool`, `tourah-mobile`

## Commands

```
sentry-cli issues list -o carinthian-technologies -p <project-slug>
sentry-cli issues list -o carinthian-technologies -p <project-slug> -s unresolved
sentry-cli events list -o carinthian-technologies -p <project-slug> --issue <issue-id>
```

`sentry-cli projects list` and `sentry-cli organizations list` need org-level scopes
the CI token typically doesn't have — use the slugs above directly instead of
discovering them.

## Notes

- "Latest issue" = top row of `issues list`, sorted by last-seen.
- Cross-reference an issue's Short ID (e.g. `TOURAH-MOBILE-4B`) in bug reports/commits —
  it's stable and links directly in the Sentry UI (`https://carinthian-technologies.sentry.io/issues/<short-id>`).
