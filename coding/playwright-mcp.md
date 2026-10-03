# Playwright MCP

Drive a real browser via the Playwright MCP: navigate, inspect, fill forms, click, screenshot.
Useful for verifying UI changes, debugging live issues, and manual E2E testing.

## Load the tools

```
ToolSearch: select:mcp__playwright__browser_navigate,mcp__playwright__browser_snapshot,mcp__playwright__browser_take_screenshot,mcp__playwright__browser_click,mcp__playwright__browser_type,mcp__playwright__browser_fill_form,mcp__playwright__browser_wait_for,mcp__playwright__browser_console_messages
```

If the tools don't appear, ask the user to confirm the MCP server is connected.

## Tools

| Tool | Use |
|---|---|
| `browser_navigate` | Go to a URL |
| `browser_snapshot` | Accessibility tree — use this to find selectors, not screenshots |
| `browser_take_screenshot` | Visual capture. Write to `/tmp` unless told otherwise |
| `browser_click` / `browser_type` | Interact with an element |
| `browser_fill_form` | Fill multiple fields at once |
| `browser_wait_for` | Wait for text/disappearance or a timeout |
| `browser_console_messages` | Check for errors after a mutation |

## Workflow

1. Navigate, then snapshot to find selectors.
2. Interact using role-based selectors — stable and match the snapshot output:
   `role=button[name="Save"]`. Avoid CSS class selectors (framework-generated hashes are
   unstable); fall back to `nth=` or a label when roles are ambiguous.
3. Check console messages after any mutation — catches 400s/500s.
4. Screenshot for visual confirmation.
