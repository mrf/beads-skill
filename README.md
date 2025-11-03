# Beads Skill for Claude Code

This skill enables Claude Code to work effectively with [Beads](https://github.com/steveyegge/beads), a lightweight git-backed issue tracker that provides persistent memory for AI coding agents.

**Latest Beads Version:** v0.21.5 (November 2025)

## Recent Updates

### Major Changes (v0.20.1+)
- **Hash-based Issue IDs**: Eliminates merge conflicts with collision-resistant IDs (bd-a1b2 vs bd-1)
- **Hierarchical Child IDs**: Support for nested work breakdown (bd-a3f8e9.1, bd-a3f8e9.3.1)
- **New Commands**: `bd migrate`, `bd doctor`, `bd daemons` for better management

### Latest Release (v0.21.5)
- Fixed double JSON encoding bug in daemon RPC calls
- Normalized `--acceptance` flag behavior
- Documentation for multiple bd binaries in PATH

## What's Included

### Skill
- `SKILL.md` - Complete reference for Beads commands and agent integration patterns
- `PERMISSIONS.md` - Guide for setting up Claude Code permissions for bd commands

### Slash Commands
Installed as Claude Code slash commands:

- `/beads:init` - **Initialize beads with permissions** - Sets up beads and configures Claude Code permissions
- `/beads:ready` - Show ready issues (no blockers)
- `/beads:create-issue` - Create a new issue with prompts
- `/beads:show` - Display issue details
- `/beads:plan` - Plan complex tasks with dependencies
- `/beads:start-work` - Start working on an issue
- `/beads:finish-work` - Complete issues and check newly unblocked work
- `/beads:status` - Show project status dashboard
- `/beads:deps` - Manage issue dependencies
- `/beads:pm` - **Project Manager** - Comprehensive audit ensuring beads-reality alignment

### Scripts
Located in `scripts/`:

- `check-version.sh` - Check for beads updates and migration needs
- `setup-permissions.sh` - Configure Claude Code permissions for bd commands
- `init-project.sh` - Initialize Beads in a new project with starter issues
- `daily-standup.sh` - Generate daily standup report
- `file-bug.sh` - Quick bug filing helper

### Templates
Located in `templates/`:

- `feature-plan.md` - Template for planning features
- `bug-template.md` - Guide for filing detailed bugs

## Installation

### Prerequisites

Install Beads:
```bash
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash
```

Or from source (requires Go):
```bash
git clone https://github.com/steveyegge/beads.git
cd beads
go install
```

### Verify Installation

```bash
bd version
bd quickstart
```

### Link Commands to Claude Code

To make the slash commands available in Claude Code, create a symlink from the skill's commands directory to your Claude Code commands folder:

```bash
ln -s ~/.claude/skills/beads/commands/beads ~/.claude/commands/beads
```

This creates a symbolic link that allows Claude Code to access all the `/beads:*` slash commands without duplicating files. After creating the symlink, you can use commands like `/beads:ready`, `/beads:create-issue`, etc. in your Claude Code sessions.

### Link Beads PM Agent

To make the Beads Project Manager agent available in Claude Code, create a symlink to the agent definition:

```bash
# Create agents directory if it doesn't exist
mkdir -p ~/.claude/agents

# Symlink the Beads PM agent
ln -s ~/.claude/skills/beads/AGENT_PM.md ~/.claude/agents/AGENT_PM.md
```

This allows Claude Code's Task tool to access the `beads-pm` agent type for comprehensive project management and beads-reality alignment checks. The agent can be invoked directly through the Task tool with `subagent_type: "beads-pm"` or via the `/beads:pm` slash command.

## Upgrading from Older Versions

### Check Your Version
```bash
~/.claude/skills/beads/scripts/check-version.sh
```

This will:
- Show your installed version vs. latest release
- Alert if you need to migrate to hash-based IDs
- Provide upgrade instructions

### Migrating to Hash-Based IDs

If you're upgrading from pre-v0.20.1 (sequential IDs like bd-1, bd-2):

```bash
# 1. Check what will be migrated
bd migrate --inspect

# 2. Test migration safety
bd migrate --dry-run

# 3. Perform migration (creates automatic backup)
bd migrate

# 4. Verify health
bd doctor
```

**Important**: Hash-based IDs eliminate merge conflicts when multiple agents work concurrently. Migration is one-way but creates backups.

## Quick Start

### Initialize Beads in your project:

**Option 1: Use the Claude Code slash command (Recommended)**
```
/beads:init
```
This will initialize beads AND automatically configure permissions in `.claude/settings.local.json`.

**Option 2: Use the command line**
```bash
bd init
~/.claude/skills/beads/scripts/setup-permissions.sh
```

**Option 3: Use the project setup script**
```bash
~/.claude/skills/beads/scripts/init-project.sh
```
Then run the permissions script to configure Claude Code.

### Use slash commands in Claude Code:
```
/beads:ready                        # See what's ready to work on
/beads:create-issue "Add OAuth"     # Create a new issue
/beads:start-work bd-1              # Start working on issue
/beads:finish-work bd-1             # Complete issue
/beads:status                       # View project status
```

## Usage Patterns

### For Agents

The skill automatically helps Claude Code:
- Remember discovered work across sessions
- Track dependencies between tasks
- Identify ready work when starting a session
- File bugs found during development
- Plan complex features with proper task breakdown

#### Beads Project Manager

The `/beads:pm` command activates a detail-oriented project manager agent that:
- **Enforces alignment** between beads issues and actual work state
- **Validates priorities** ensuring highest priority work is tackled first
- **Captures all work** by finding untracked todos, commits, and code comments
- **Syncs TodoWrite** ensuring every todo has a corresponding beads issue
- **Maintains discipline** by preventing work outside of beads tracking

Use it for:
- Regular project health checks
- Ensuring team works on what matters most
- Finding and filing untracked work
- Validating TodoWrite and beads are in sync
- Getting a comprehensive status overview

See `AGENT_PM.md` for the complete project manager specification.

### Command Reference

See `SKILL.md` for complete command reference. Key commands:

```bash
# View work
bd ready --json              # Unblocked issues
bd list --status open        # All open issues
bd show bd-1                 # Issue details

# Create work
bd create "Title" -t bug -p 1 -l "security"
bd create -f plan.md         # From template

# Update work
bd update bd-1 -s in_progress
bd close bd-1

# Dependencies
bd dep add bd-5 bd-3         # bd-5 depends on bd-3
bd dep tree bd-1             # Show tree
```

## New Features Guide

### Hash-Based Issue IDs
- Auto-generated collision-resistant IDs: `bd-a1b2`, `bd-f14c`
- Eliminates merge conflicts in multi-agent workflows
- No more manual ID coordination

### Hierarchical Child IDs
```bash
# Create epic
EPIC=$(bd create "Epic: User Management" -t epic --json | jq -r '.id')

# Create child tasks (auto-numbered)
bd create "Add login form" --parent $EPIC  # Creates bd-a3f8.1
bd create "Add signup form" --parent $EPIC # Creates bd-a3f8.2
bd create "Add password reset" --parent $EPIC # Creates bd-a3f8.3
```

### Health Monitoring
```bash
# Regular health checks
bd doctor              # Check setup and database
bd daemons health      # Check daemon status
bd migrate --inspect   # Check if migration needed
```

## Tips

1. **Check version regularly** - Run `~/.claude/skills/beads/scripts/check-version.sh`
2. **Use --json flag** for programmatic parsing
3. **File issues liberally** during exploration - it's the agent's memory
4. **Use hierarchical IDs** for nested work breakdown (`--parent` flag)
5. **Model dependencies carefully** - first arg depends on second arg in `bd dep add`
6. **Check ready work** at session start - it's the agent's todo list
7. **Use labels** for organization and filtering
8. **Run health checks** periodically with `bd doctor`

## Resources

- Beads GitHub: https://github.com/steveyegge/beads
- Beads CLI Help: `bd --help`
- Command Help: `bd [command] --help`
- Quickstart: `bd quickstart`

## Making Scripts Executable

```bash
chmod +x ~/.claude/skills/beads/scripts/*.sh
```

## Troubleshooting

**Beads not found**: Ensure `bd` is in your PATH
**Database not found**: Run `bd init` in project root
**Old ID format**: Run `bd migrate` to upgrade to hash-based IDs
**Sync conflicts**: Run any bd command to auto-merge
**Performance issues**: Use `bd daemon &` for background sync
**Health issues**: Run `bd doctor` to diagnose and fix
**Daemon problems**: Run `bd daemons killall` to reset
**Outdated version**: Run `~/.claude/skills/beads/scripts/check-version.sh`

## License

This skill is provided as-is. Beads is MIT licensed.
