---
description: Manage dependencies between Beads issues
argument-hint: <dependent-id> <dependency-id>
allowed-tools: Bash(bd:*)
---

Manage dependencies between Beads issues.

If two issue IDs are provided in $ARGUMENTS (e.g., `bd-5 bd-3`):
- Add a dependency: the first issue depends on the second
- Run: `bd dep add [dependent] [dependency]`
- Show the dependency tree: `bd dep tree [dependent]`

If one issue ID is provided:
- Show its dependency tree: `bd dep tree [issue-id]`

If no IDs are provided, ask what the user wants to do:
- Add a dependency between two issues
- View dependency tree for an issue
- Remove a dependency
- Check for circular dependencies with `bd dep cycles`

Clearly explain the relationship direction: "bd-5 depends on bd-3" means bd-3 must be completed before bd-5 can be finished.
