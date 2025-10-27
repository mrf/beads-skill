---
description: Show Beads project status overview
allowed-tools: Bash(bd:*)
---

Show a comprehensive overview of the Beads project status.

Run these commands and present a clear dashboard:

1. `bd stats` - Overall statistics
2. `bd ready --json --limit 5` - Top ready issues
3. `bd list --status in_progress --json` - Work in progress
4. `bd blocked --json` - Blocked issues
5. `bd list --priority 0 --status open --json` - Critical open issues

Format the output as a status dashboard with sections for:
- **Statistics**: Total issues, by status, by priority
- **Ready to Work** (top 5): Issues with no blockers
- **In Progress**: Currently being worked
- **Blocked**: Issues waiting on dependencies
- **Critical**: Highest priority open items

Provide actionable insights based on the data.
