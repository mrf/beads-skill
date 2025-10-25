# Beads Skill for Claude Code

This skill enables Claude Code to work effectively with [Beads](https://github.com/steveyegge/beads), a lightweight git-backed issue tracker that provides persistent memory for AI coding agents.

## What's Included

### Skill
- `SKILL.md` - Complete reference for Beads commands and agent integration patterns

### Slash Commands
Installed as Claude Code slash commands:

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

- `init-project.sh` - Initialize Beads in a new project
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

## Quick Start

### Initialize Beads in your project:
```bash
bd init
```

Or use the helper script that sets up starter issues:
```bash
~/.claude/skills/beads/scripts/init-project.sh
```

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

## Tips

1. **Use --json flag** for programmatic parsing
2. **File issues liberally** during exploration - it's the agent's memory
3. **Model dependencies carefully** - first arg depends on second arg in `bd dep add`
4. **Check ready work** at session start - it's the agent's todo list
5. **Use labels** for organization and filtering
6. **Run daily standup** for project health overview

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
**Sync conflicts**: Run any bd command to auto-merge
**Performance issues**: Use `bd daemon &` for background sync

## License

This skill is provided as-is. Beads is MIT licensed.
