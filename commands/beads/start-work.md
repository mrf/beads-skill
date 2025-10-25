---
description: Start working on a Beads issue
argument-hint: <issue-id>
allowed-tools: Bash(bd:*)
---

Mark a Beads issue as in progress and assign it to yourself.

1. If an issue ID is provided in $ARGUMENTS, use it. Otherwise, show ready work with `bd ready --json --limit 10` and ask which issue to start.

2. Show the issue details with `bd show [issue-id] --json`.

3. Update the issue:
   - Set status to in_progress
   - Assign to the current user (use $USER or $BD_ACTOR)
   - Run: `bd update [issue-id] -s in_progress -a [user] --json`

4. Show a confirmation with the issue details and any dependencies that might need attention.

5. If the issue has dependencies that are not closed, warn about them.
