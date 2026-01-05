# Beads Skill for Claude Code

This was an early experiment and you will probably have a lot better experience using the [official Beads Claude plugin](https://github.com/steveyegge/beads/blob/main/docs/PLUGIN.md)

This skill enables Claude Code to work effectively with [Beads](https://github.com/steveyegge/beads), a lightweight git-backed issue tracker that provides persistent memory for AI coding agents.

## What Is This?

The Beads skill provides:
- **Slash commands** for common beads operations (`/beads:ready`, `/beads:create-issue`, etc.)
- **Agent integration** that helps Claude remember work across sessions
- **Project Manager agent** that ensures beads issues match reality
- **Complete command reference** for all beads CLI operations

## Quick Start

### 1. Install Beads

```bash
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash
```

Or from source (requires Go):
```bash
git clone https://github.com/steveyegge/beads.git
cd beads && go install
```

Verify: `bd version` (should show v0.21.5 or later)

### 2. Link Commands to Claude Code

Make slash commands available:

```bash
ln -s ~/.claude/skills/beads/commands/beads ~/.claude/commands/beads
```

### 3. Link the Beads PM Agent (Optional)

To use the Project Manager agent:

```bash
mkdir -p ~/.claude/agents
ln -s ~/.claude/skills/beads/AGENT_PM.md ~/.claude/agents/AGENT_PM.md
```

### 4. Initialize in Your Project

Use the slash command (automatically sets permissions):

```
/beads:init
```

Or manually:

```bash
bd init
~/.claude/skills/beads/scripts/setup-permissions.sh
```

## Using the Skill

### Basic Workflow

```
# See what's ready to work on
/beads:ready

# Create a new issue
/beads:create-issue "Add OAuth support"

# Start working on an issue
/beads:start-work bd-a1b2

# Complete work
/beads:finish-work bd-a1b2

# Check project status
/beads:status
```

### Available Slash Commands

| Command | Purpose |
|---------|---------|
| `/beads:init` | Initialize beads and configure permissions |
| `/beads:ready` | Show issues with no blockers |
| `/beads:create-issue` | Create a new issue with prompts |
| `/beads:show` | Display issue details |
| `/beads:plan` | Plan complex tasks with dependencies |
| `/beads:start-work` | Start working on an issue |
| `/beads:finish-work` | Complete issues and check newly unblocked work |
| `/beads:status` | Show project status dashboard |
| `/beads:deps` | Manage issue dependencies |
| `/beads:pm` | Run Project Manager audit |

### How Claude Uses Beads

The skill automatically helps Claude:
- **Remember work** across conversation sessions
- **Track dependencies** between tasks
- **Identify ready work** when starting
- **File bugs** found during development
- **Plan features** with proper task breakdown

Just work naturally - Claude will use beads to maintain context and memory.

## Using the Project Manager Agent

The Beads PM agent ensures perfect alignment between what's in beads and what's actually happening in your project.

### Quick Health Check

```
/beads:pm
```

This runs a comprehensive audit and reports:
- Current beads state (issues by status/priority)
- Alignment issues (missing beads issues, status mismatches)
- Priority violations (low priority work while high priority waits)
- Specific next actions to take

### When to Use It

- **Starting a work session** - Get aligned before diving in
- **After significant changes** - Ensure everything is tracked
- **When feeling scattered** - Get clarity on priorities
- **Before standups** - Know what's actually happening
- **When priorities shift** - Validate the work queue

### What the PM Does

The Project Manager:
- **Enforces alignment** between beads issues and actual work state
- **Validates priorities** ensuring highest priority work is tackled first
- **Captures all work** by finding untracked todos, commits, and code comments
- **Syncs TodoWrite** ensuring every todo has a corresponding beads issue
- **Maintains discipline** by preventing work outside of beads tracking

### Example PM Session

```
/beads:pm
```

**Output:**
```
🎯 BEADS PROJECT STATUS

📊 Statistics
- Total: 15 issues (8 open, 2 in_progress, 1 blocked, 4 closed)
- Ready: 5 issues (P0: 1, P1: 2, P2: 2)

🚨 PRIORITY VIOLATIONS
- bd-42 (P0) "Fix login security flaw" is ready but not being worked
- bd-55 (P2) "Improve UI animations" is in_progress

ACTION: Stop bd-55, start bd-42 immediately

✅ NEXT REQUIRED ACTIONS
1. bd update bd-55 -s open
2. bd update bd-42 -s in_progress -a @me
3. Begin work on bd-42
```

The PM ensures you work on what matters most and nothing falls through cracks.

### Integration with TodoWrite

The PM automatically syncs TodoWrite todos with beads:

**Before:**
- TodoWrite has "Add dark mode" (pending)
- Beads has no matching issue

**PM identifies:** "Add dark mode" isn't tracked!

**After:**
- TodoWrite: "Add dark mode (pending) → bd-61"
- Beads: bd-61 "Add dark mode" (open)
- All aligned!

### PM Invocation Options

**Quick Check (Slash Command):**
```
/beads:pm
```

**Agent Mode (for ongoing sessions):**
```
Please act as the Beads Project Manager and help maintain project discipline during this session
```

In agent mode, the PM will proactively monitor work and interrupt when it spots misalignment.

## Command Reference

See [SKILL.md](./SKILL.md) for the complete beads CLI reference.

Key patterns:

```bash
# View work
bd ready --json              # Unblocked issues
bd list --status open        # All open issues
bd show bd-a1b2              # Issue details

# Create work
bd create "Title" -t bug -p 1 -l "security"
bd create "Subtask" --parent bd-a3f8  # Creates bd-a3f8.1

# Update work
bd update bd-a1b2 -s in_progress
bd close bd-a1b2

# Dependencies
bd dep add bd-5 bd-3         # bd-5 depends on bd-3
bd dep tree bd-1             # Show tree
```

## Key Features

### Hash-Based Issue IDs
- Auto-generated collision-resistant IDs: `bd-a1b2`, `bd-f14c`
- Eliminates merge conflicts in multi-agent workflows
- No more manual ID coordination

### Hierarchical Child IDs
```bash
EPIC=$(bd create "Epic: User Management" -t epic --json | jq -r '.id')
bd create "Add login form" --parent $EPIC  # Creates bd-a3f8.1
bd create "Add signup form" --parent $EPIC # Creates bd-a3f8.2
```

### Health Monitoring
```bash
bd doctor              # Check setup and database
bd daemons health      # Check daemon status
bd migrate --inspect   # Check if migration needed
```

## Upgrading

### Check Your Version

```bash
~/.claude/skills/beads/scripts/check-version.sh
```

Shows installed version vs. latest release and migration needs.

### Migrating to Hash-Based IDs

If upgrading from pre-v0.20.1 (sequential IDs like bd-1, bd-2):

```bash
# Check what will be migrated
bd migrate --inspect

# Test migration safety
bd migrate --dry-run

# Perform migration (creates automatic backup)
bd migrate

# Verify health
bd doctor
```

## Additional Resources

### Scripts

Located in `scripts/`:
- `check-version.sh` - Check for beads updates
- `setup-permissions.sh` - Configure Claude Code permissions
- `init-project.sh` - Initialize beads with starter issues
- `daily-standup.sh` - Generate standup report
- `file-bug.sh` - Quick bug filing

Make executable: `chmod +x ~/.claude/skills/beads/scripts/*.sh`

### Templates

Located in `templates/`:
- `feature-plan.md` - Template for planning features
- `bug-template.md` - Guide for filing detailed bugs

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Beads not found | Ensure `bd` is in your PATH |
| Database not found | Run `bd init` in project root |
| Old ID format | Run `bd migrate` to upgrade |
| Sync conflicts | Run any `bd` command to auto-merge |
| Performance issues | Use `bd daemon &` for background sync |
| Health issues | Run `bd doctor` to diagnose |
| Daemon problems | Run `bd daemons killall` to reset |
| Outdated version | Run check-version.sh script |
| Permission prompts | Run `/beads:init` or setup-permissions.sh |

## Tips

1. Check version regularly with `check-version.sh`
2. Use `--json` flag for programmatic parsing
3. File issues liberally - it's the agent's memory
4. Use hierarchical IDs for nested work (`--parent` flag)
5. Model dependencies carefully - first arg depends on second
6. Check ready work at session start
7. Use labels for organization
8. Run health checks periodically with `bd doctor`
9. Let Claude use beads naturally - don't micromanage

## External Links

- [Beads GitHub](https://github.com/steveyegge/beads)
- [Beads CLI Help](https://github.com/steveyegge/beads#cli-reference): `bd --help`
- [Quickstart Guide](https://github.com/steveyegge/beads#quickstart): `bd quickstart`

## License

This skill is provided as-is. Beads is MIT licensed.
