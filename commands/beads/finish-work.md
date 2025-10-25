---
description: Mark Beads issue(s) as complete
argument-hint: <issue-id> [issue-id...]
allowed-tools: Bash(bd:*)
---

Close one or more Beads issues and check what becomes unblocked.

1. If issue IDs are provided in $ARGUMENTS, use them. Otherwise, show current in-progress work with `bd list --status in_progress --json` and ask which to close.

2. Show details of the issue(s) to be closed.

3. Close the issue(s): `bd close [issue-ids]`

4. Check what work became ready: `bd ready --json --limit 10`

5. Present a summary showing:
   - Issues closed
   - Newly unblocked work (if any)
   - Next suggested tasks to work on
