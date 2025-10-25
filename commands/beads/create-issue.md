---
description: Create a new Beads issue with details
argument-hint: [title]
allowed-tools: Bash(bd:*)
---

Create a new issue in Beads. If a title is provided in $ARGUMENTS, use it. Otherwise, ask the user for:
1. Issue title
2. Description (optional)
3. Type: bug, feature, task, epic, or chore
4. Priority: 0 (critical), 1 (high), 2 (medium), 3 (low), or 4 (backlog)
5. Labels (comma-separated, optional)
6. Dependencies (other issue IDs, optional)

Then run `bd create` with the appropriate flags and show the created issue details from the JSON output.
