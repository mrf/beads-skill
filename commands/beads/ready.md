---
description: Show ready Beads issues (no blockers)
argument-hint: [--priority N] [--assignee NAME]
allowed-tools: Bash(bd:*)
---

Show Beads issues that are ready to work on (no blockers). Use the optional arguments to filter results.

Run `bd ready --json --limit 20 $ARGUMENTS` and present the results in a clear, actionable format. For each ready issue, show:
- Issue ID and title
- Priority level
- Assignee (if any)
- Type (bug/feature/task/etc)
- Labels

If there are no ready issues, suggest checking `bd list --status open` or creating new work with `bd create`.
