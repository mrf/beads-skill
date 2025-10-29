# Beads Permissions Setup

## Why Permissions Are Needed

Claude Code requires explicit permission to run `bd` commands through the Bash tool. Without these permissions configured, each command will prompt for approval, which disrupts the workflow.

## Automatic Setup (Recommended)

Use the `/beads:init` slash command in any project:

```
/beads:init
```

This will:
1. Initialize beads if not already done
2. Create or update `.claude/settings.local.json` with the required permissions
3. Preserve any existing settings

## Manual Setup

If you prefer to set up permissions manually, add this to your project's `.claude/settings.local.json`:

```json
{
  "permissions": {
    "allow": [
      "Bash(bd :*)",
      "SlashCommand(/beads:*)"
    ]
  }
}
```

## Command Line Setup

Run the setup script from your project directory:

```bash
~/.claude/skills/beads/scripts/setup-permissions.sh
```

This script will:
- Create `.claude/` directory if needed
- Create or update `settings.local.json`
- Merge with existing permissions if present
- Requires `jq` for JSON manipulation

## What Gets Permitted

The configuration allows Claude Code to run:
- `bd:*` - All beads CLI commands (bd:list, bd:create, bd:show, etc.)
- `/beads:*` - All beads slash commands (/beads:ready, /beads:status, /beads:plan, etc.)

## Per-Project vs Global

These permissions are set at the **project level** in `.claude/settings.local.json`, which:
- Applies only to the current project
- Is typically gitignored (check your `.gitignore`)
- Allows different permission settings per project
- Keeps your global Claude Code settings clean

## Troubleshooting

**Still getting permission prompts?**
- Ensure `.claude/settings.local.json` exists in your project root
- Check that the JSON is valid (use `jq . .claude/settings.local.json`)
- Restart Claude Code after updating permissions
- Check for typos in the pattern strings

**Script fails with jq error?**
- Install jq: `brew install jq` (macOS) or equivalent
- Or manually edit `.claude/settings.local.json`

**Permissions not taking effect?**
- The settings file must be in your project root
- Pattern matching is case-sensitive
- Try restarting your Claude Code session

## Security Note

These permissions only allow Claude Code to run `bd` commands - they don't grant filesystem access or other elevated privileges. The `bd` commands themselves operate within the normal constraints of your user account and the beads database in your project.
