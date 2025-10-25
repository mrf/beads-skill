---
description: Plan a complex task with Beads issues and dependencies
argument-hint: [epic-title]
allowed-tools: Bash(bd:*)
---

Help the user plan a complex task by breaking it down into Beads issues with dependencies.

1. If an epic title is provided in $ARGUMENTS, use it. Otherwise, ask the user what feature/project they want to plan.

2. Break down the work into logical subtasks. For each subtask, ask if the user wants to include it.

3. Create the epic: `bd create "Epic: [title]" -t epic -p [priority] --json`

4. Create each subtask with:
   - Appropriate type (task, feature, bug)
   - Dependencies on the epic and other tasks where needed
   - Reasonable priority

5. Add any blocking relationships between tasks using `bd dep add`.

6. Show the final plan with: `bd dep tree [epic-id]`

Present a clear summary showing all created issues and their relationships.
