---
name: beads
description: Work with Beads issue tracker for AI agent memory. Use when managing tasks, tracking dependencies, filing issues, or maintaining context across coding sessions. Handles bd commands, issue graphs, and persistent agent memory.
---

# Beads: Memory System for Coding Agents

Beads is a lightweight, git-backed issue tracking system that provides persistent memory for AI coding agents across sessions.

## Core Capabilities

### Issue Management
- Create, update, and close issues with rich metadata
- Track dependencies between issues (blocks, discovered-from, etc.)
- Automatically identify "ready" work (issues with no open blockers)
- Filter by status, priority, assignee, labels, and type

### Agent Memory Integration
- Persist discovered work across conversation sessions
- Build dependency graphs for complex nested tasks
- File issues automatically during exploration
- Resume long-horizon tasks with full context

### Git-Based Sync
- Issues stored as JSONL files in `.beads/` directory
- Committed to git like any other code artifact
- Local SQLite cache for fast queries
- Optional daemon for background sync
- No external servers required

## Setup

### Permissions Configuration
When first using Beads commands, configure project permissions to allow all `bd` commands without prompting:

1. Check if settings.json exists and has permissions configured
2. If not, add the following to the project's `.claude/settings.json`:
```json
{
  "permissions": {
    "allow": [
      "Bash(bd:*)"
    ]
  }
}
```

This ensures smooth operation of all Beads commands without repeated permission prompts.

## Common Workflows

### Starting a Session
1. Check for ready work: `bd ready --json`
2. Review issue details: `bd show bd-1`
3. Assign to yourself: `bd update bd-1 -a @me`
4. Mark in progress: `bd update bd-1 -s in_progress`

### During Development
1. Discover new issues: `bd create "Title" -d "Details" -t bug -p 1`
2. Link dependencies: `bd dep add bd-2 bd-1` (bd-2 depends on bd-1)
3. Update status: `bd update bd-1 -s blocked`
4. Add labels: `bd label add bd-1 security`

### Completing Work
1. Mark done: `bd close bd-1`
2. Commit changes (includes Beads metadata in .beads/ directory)
3. Check for newly unblocked work: `bd ready --json`

### Complex Task Planning
1. Create parent epic: `bd create "Epic: Feature Name" -t epic -p 0`
2. Create child tasks: `bd create "Subtask" -t task --deps "bd-1"`
3. Add blockers: `bd dep add bd-3 bd-2` (bd-3 depends on bd-2)
4. Visualize: `bd dep tree bd-1`

## Best Practices for Agents

### When to File Issues
- Discovered technical debt during exploration
- Found bugs or edge cases while implementing features
- Identified related work that's out of current scope
- Need to remember context for future sessions

### Dependency Modeling
When using `bd dep add [dependent] [dependency]`:
- The first issue depends on the second
- The second issue must be done before the first
- Example: `bd dep add bd-5 bd-3` means "bd-5 depends on bd-3"

Use `--deps` flag during creation:
- `bd create "Task" --deps "discovered-from:bd-20,blocks:bd-15"`
- Or simple format: `--deps "bd-20,bd-15"`

### Working with Ready Issues
```bash
# Get machine-readable list of unblocked work
bd ready --json --limit 20

# Filter by priority
bd ready --priority 0  # Critical only

# Filter by assignee
bd ready --assignee alice

# Show all open issues
bd list --status open --json
```

### Maintaining Clean State
- Close completed issues: `bd close bd-1 bd-2 bd-3`
- Remove invalid blockers: `bd dep remove bd-2 bd-1`
- Reopen if needed: `bd reopen bd-1`
- Use labels for categorization: `bd label add bd-1 refactor security`

## CLI Reference

### Core Commands

#### Initialization
```bash
bd init                    # Initialize bd in current project
bd onboard                 # Interactive agent onboarding
bd quickstart              # Interactive tutorial
```

#### Creating Issues
```bash
bd create "Title"                              # Basic creation
bd create "Title" -d "Description"             # With description
bd create "Title" -t bug -p 1                  # Set type and priority
bd create "Title" -a alice -l backend,urgent   # Assign and label
bd create "Title" --deps "bd-20,bd-15"         # With dependencies
bd create "Title" --id worker1-100             # Explicit ID
bd create -f plan.md                           # From markdown file
bd create "Title" --json                       # JSON output
```

**Types**: bug, feature, task, epic, chore
**Priorities**: 0=critical, 1=high, 2=medium (default), 3=low, 4=backlog

#### Viewing Issues
```bash
bd show bd-1                           # Full details of one issue
bd show bd-1 bd-2 bd-3                 # Multiple issues
bd list                                # All issues
bd list --status open                  # Filter by status
bd list --priority 1                   # Filter by priority (0-4)
bd list --assignee alice               # Filter by assignee
bd list --label backend,urgent         # AND logic (all labels)
bd list --label-any frontend,backend   # OR logic (any label)
bd list --type bug                     # Filter by type
bd list --title "auth"                 # Text search in title
bd list --limit 50                     # Limit results
bd list --json                         # JSON output
```

**Statuses**: open, in_progress, blocked, closed

#### Updating Issues
```bash
bd update bd-1 -s in_progress          # Change status
bd update bd-1 -p 0                    # Change priority
bd update bd-1 -a bob                  # Reassign
bd update bd-1 --title "New Title"     # Update title
bd update bd-1 -d "New description"    # Update description
bd update bd-1 bd-2 bd-3 -s closed     # Bulk update
bd update bd-1 --json                  # JSON output
```

#### Closing Issues
```bash
bd close bd-1                          # Close one issue
bd close bd-1 bd-2 bd-3                # Close multiple
bd close bd-1 --reason "Completed"     # With reason
bd reopen bd-1                         # Reopen closed issue
```

#### Dependencies
```bash
bd dep add bd-5 bd-3                   # bd-5 depends on bd-3
bd dep add bd-5 bd-3 --type blocks     # Explicit type
bd dep remove bd-5 bd-3                # Remove dependency
bd dep tree bd-2                       # Show dependency tree
bd dep cycles                          # Detect circular deps
```

**Note**: In `bd dep add [A] [B]`, issue A depends on issue B (B must finish first)

#### Finding Work
```bash
bd ready                               # Issues with no blockers
bd ready --limit 20                    # Limit results
bd ready --priority 1                  # High priority only
bd ready --assignee alice              # Assigned to alice
bd ready --json                        # JSON output
bd blocked                             # Show blocked issues
bd stats                               # Statistics
```

#### Labels
```bash
bd label add bd-42 security            # Add single label
bd label add bd-42 bug urgent          # Add multiple labels
bd label remove bd-42 urgent           # Remove label
bd label list bd-42                    # Labels on issue
bd label list-all                      # All labels with counts
```

#### Deletion
```bash
bd delete bd-1                         # Preview mode
bd delete bd-1 --force                 # Force delete
bd delete bd-1 bd-2 bd-3 --force       # Batch delete
bd delete bd-1 --cascade --force       # Delete with dependents
```

#### Comments
```bash
bd comments bd-1                       # View comments
bd comments bd-1 "Add comment"         # Add comment
```

#### Configuration
```bash
bd config set jira.url "https://..."   # Set config value
bd config get jira.url                 # Get value
bd config list --json                  # List all config
bd config unset jira.url               # Remove config
```

#### Export/Import
```bash
bd export -o issues.jsonl              # Export to JSONL
bd import -i issues.jsonl              # Import from JSONL
bd compact --days 90                   # Compact old issues
bd sync                                # Manual git sync
```

### Global Flags

Available on all commands:
```bash
--json                  # JSON output
--actor "name"          # Override actor name
--db "/path/to/db"      # Override database path
--no-daemon             # Bypass daemon
--no-auto-flush         # Disable auto-sync
--no-auto-import        # Disable auto-import
--sandbox               # All no-* flags combined
```

## JSON Output Format

All commands support `--json` flag:

```json
{
  "id": "bd-42",
  "title": "Implement OAuth login",
  "description": "Add OAuth 2.0 support",
  "status": "in_progress",
  "priority": 1,
  "type": "feature",
  "assignee": "alice",
  "labels": ["auth", "security"],
  "created_at": "2025-01-15T10:30:00Z",
  "updated_at": "2025-01-15T14:22:00Z",
  "closed_at": null,
  "dependencies": ["bd-40"],
  "dependents": ["bd-43", "bd-44"],
  "external_ref": "gh-123"
}
```

## Agent Integration Patterns

### Session Initialization
```bash
# Check if Beads is initialized
if [ -d ".beads" ]; then
    echo "Beads memory available"
    bd ready --json --limit 10
else
    echo "No Beads database found. Run 'bd init' to initialize."
fi
```

### Auto-Filing Discovered Work
```bash
# When finding a bug during implementation
bd create "Fix null pointer in auth handler" \
    -d "Found in auth.go:142 during login feature work" \
    -t bug \
    -p 1 \
    -l "bug,backend" \
    --deps "discovered-from:bd-123" \
    --json
```

### Task Planning Template
```bash
# Break down large feature
EPIC_ID=$(bd create "Epic: User Dashboard" -t epic -p 1 --json | jq -r '.id')
bd create "Design dashboard layout" -t task --deps "$EPIC_ID"
bd create "Implement data fetching" -t task --deps "$EPIC_ID"
bd create "Add filtering controls" -t task --deps "$EPIC_ID"
bd create "Write integration tests" -t task --deps "$EPIC_ID"

# Add inter-task dependencies
bd dep add bd-3 bd-2  # bd-3 depends on bd-2
bd dep add bd-5 bd-3  # bd-5 depends on bd-3
```

### Querying Ready Work
```bash
# Get top priority unblocked work
READY=$(bd ready --priority 0 --json --limit 5)
echo "$READY" | jq -r '.[] | "\(.id): \(.title)"'

# Get assigned work for agent
bd list --assignee "@agent" --status in_progress --json
```

## Installation

```bash
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash
```

Or install from source (requires Go):
```bash
git clone https://github.com/steveyegge/beads.git
cd beads
go install
```

Verify installation:
```bash
bd version
bd quickstart
```

## Troubleshooting

### Sync Conflicts
If git conflicts occur in `.beads/` directory:
```bash
# Beads auto-merges on next operation
bd list  # Triggers merge
```

### Missing Dependencies
```bash
# Check Go installation (if building from source)
go version

# Verify bd in PATH
which bd

# Check version
bd version
```

### Performance Issues
```bash
# Use daemon for better performance
bd daemon &

# Check database path
bd config get db.path

# Compact old issues
bd compact --days 90 --dry-run
```

### Common Errors

**"Issue not found"**: Check ID format (should be like `bd-1`, `bd-42`)
**"Dependency cycle detected"**: Use `bd dep cycles` to find circular dependencies
**"Database not found"**: Run `bd init` in project root

## References

- GitHub: https://github.com/steveyegge/beads
- Installation: https://github.com/steveyegge/beads#installation
- CLI Reference: `bd --help`
- Command Help: `bd [command] --help`
