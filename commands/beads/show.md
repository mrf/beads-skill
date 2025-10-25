---
description: Show details of Beads issue(s)
argument-hint: <issue-id> [issue-id...]
allowed-tools: Bash(bd:*)
---

Show detailed information about one or more Beads issues.

Run `bd show $ARGUMENTS --json` and present the results clearly, including:
- Issue ID, title, and description
- Status, priority, and type
- Assignee and labels
- Created/updated timestamps
- Dependencies (what this blocks and what blocks this)
- External references

If no issue ID is provided, ask the user which issue they want to see.
