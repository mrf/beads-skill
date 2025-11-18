---
description: Initialize beads in project with permissions
allowed-tools: Bash(bd:*), Read, Write
---

Initialize Beads issue tracker in the current project and configure Claude Code permissions.

Follow these steps:

1. Check if .beads directory already exists using the Glob tool:
   ```
   Glob pattern=".beads"
   ```

   If the .beads directory is found in the results, it exists.

2. If not found, initialize beads:
   ```bash
   bd init
   ```

3. Create or update `.claude/settings.local.json` with permissions for all bd commands.

   First, check if `.claude/settings.local.json` exists and read it if present.

   Then, merge in the following permissions configuration:

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

   If the file exists, merge the permissions intelligently:
   - Keep existing permissions that don't conflict
   - Add the bd permissions if not already present
   - Preserve any other settings in the file

   If the file doesn't exist, create `.claude/` directory first if needed, then write the full settings file.

4. Confirm to the user:
   - Whether beads was newly initialized or already existed
   - That permissions have been configured in `.claude/settings.local.json`
   - Show the user what they can do next:
     - `/beads:ready` - See ready work
     - `/beads:create-issue` - Create a new issue
     - `/beads:status` - View project status

**Important**: Make sure to properly merge JSON when updating settings.local.json. Don't overwrite existing settings.
