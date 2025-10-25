# Beads Project Manager - Quick Start Guide

## What is it?

The Beads Project Manager is an obsessively detail-oriented agent that ensures your project's actual state matches what's tracked in beads. It acts as a vigilant guardian, preventing work from falling through cracks and ensuring everyone focuses on what matters most.

## When to use it?

- **Starting a work session** - Get aligned before diving in
- **After significant changes** - Ensure everything is tracked
- **When feeling scattered** - Get clarity on priorities
- **Before standups** - Know what's actually happening
- **When priorities shift** - Validate the work queue

## How to invoke

### Quick Check (Slash Command)
```
/beads:pm
```

This runs a comprehensive audit and reports:
- Current beads state (issues by status/priority)
- Alignment issues (missing beads issues, status mismatches)
- Priority violations (low priority work while high priority waits)
- Specific next actions to take

### Deep Session (Agent Mode)

For ongoing project management, you can ask Claude to act as the project manager:

```
Please act as the Beads Project Manager from AGENT_PM.md and help me maintain project discipline during this session
```

In this mode, the agent will:
- Monitor all work and ensure it's tracked in beads
- Interrupt when it sees untracked work
- Challenge priority decisions
- Automatically sync TodoWrite with beads
- Keep you focused on highest priority items

## Example Workflow

### 1. Session Start
```
/beads:pm
```

**Output:**
```
🎯 BEADS PROJECT STATUS

📊 Statistics
- Total: 15 issues (8 open, 2 in_progress, 1 blocked, 4 closed)
- Ready: 5 issues (P0: 1, P1: 2, P2: 2)

⚠️ CRITICAL ALIGNMENT ISSUES
None found - great work!

🚨 PRIORITY VIOLATIONS
- bd-42 (P0) "Fix login security flaw" is ready but not being worked
- bd-55 (P2) "Improve UI animations" is in_progress

ACTION: Stop bd-55, start bd-42 immediately

✅ NEXT REQUIRED ACTIONS
1. bd update bd-55 -s open
2. bd update bd-42 -s in_progress -a @me
3. Begin work on bd-42
```

### 2. Work on Correct Priority

Start the P0 issue:
```
/beads:start-work bd-42
```

### 3. Discover New Work

While fixing the security flaw, you find another bug. The PM ensures it's tracked:

**You:** "I found another issue with password validation"

**PM:** "That's not in beads. Let me file it:
```bash
bd create "Fix password validation bug" -t bug -p 1 -d "Discovered while fixing bd-42" --deps "discovered-from:bd-42"
```
Created bd-60. Continue with bd-42, we'll prioritize bd-60 after."

### 4. Complete Work

```
/beads:finish-work bd-42
```

**PM checks:**
- Is bd-42 actually complete?
- Should bd-60 be worked next?
- What new work is now unblocked?

### 5. Regular Check-ins

Every hour or after major changes:
```
/beads:pm
```

Ensures alignment hasn't drifted.

## Integration with TodoWrite

The PM automatically syncs TodoWrite todos with beads:

**Before PM:**
```
TodoWrite:
- Fix login bug (in_progress)
- Add dark mode (pending)
- Write tests (pending)

Beads:
- bd-42: Fix login security flaw (in_progress)
```

**PM identifies:** "Add dark mode" and "Write tests" aren't in beads!

**After PM:**
```
TodoWrite:
- Fix login bug (in_progress) → bd-42
- Add dark mode (pending) → bd-61
- Write tests (pending) → bd-62

All aligned! ✓
```

## Common Scenarios

### Scenario 1: Team Member Says "I'm working on X"

**PM Response:**
- Checks if X has a beads issue
- If not: Creates one
- Verifies it's the right priority
- Updates status to in_progress
- Assigns to team member

### Scenario 2: Pull Request Merged

**PM Response:**
- Asks: "What beads issue does this close?"
- Closes the issue
- Runs `bd ready` to see what's now unblocked
- Suggests next work

### Scenario 3: Standup Time

```
/beads:pm
```

**PM provides:**
- What was completed (closed issues)
- What's in progress
- What's blocked and why
- What should be worked on next

### Scenario 4: TodoWrite Diverged

**PM notices:** TodoWrite has 5 items but only 2 are in beads

**PM action:**
- Lists the 3 untracked items
- Provides exact commands to create beads issues
- Offers to create them automatically
- Updates TodoWrite to reference beads IDs

## PM Behavior Examples

### Obsessive Tracking
**You:** "I need to quickly fix this typo"
**PM:** "Even quick fixes should be tracked. Created bd-70 for you."

### Priority Enforcement
**You:** "I'm going to add that nice animation feature"
**PM:** "bd-42 (P0 security flaw) is ready. We can't work on P2 features while P0 exists. Work on bd-42 first."

### Dependency Validation
**You:** "I can't finish bd-50 until the API is deployed"
**PM:** "Let me model that dependency:
```bash
bd dep add bd-50 bd-48  # bd-50 depends on bd-48 (API deployment)
bd update bd-50 -s blocked
```
Now bd-50 correctly shows as blocked. Is there other ready work?"

### Status Accuracy
**You:** "I finished the feature yesterday"
**PM:** "bd-55 is still marked in_progress. Closing it now:
```bash
bd close bd-55
```
This unblocks bd-56 and bd-57. Ready to work on them?"

## Tips for Working with the PM

1. **Trust the process** - If PM says create an issue, create it
2. **Embrace the discipline** - Tracking everything prevents problems later
3. **Use it regularly** - Daily `/beads:pm` keeps projects healthy
4. **Let it interrupt** - When PM spots misalignment, address it immediately
5. **Question back** - If PM's priority seems wrong, discuss and adjust

## Configuration

The PM respects your beads setup but has opinions:

- **Priority levels:** Strictly enforces P0 > P1 > P2 > P3 > P4
- **Status meanings:**
  - `open` = defined but not started
  - `in_progress` = being worked on RIGHT NOW
  - `blocked` = has open dependencies
  - `closed` = completely done

- **Dependencies:** Always models as first-depends-on-second
  - `bd dep add bd-5 bd-3` means bd-5 can't start until bd-3 is done

## Advanced Usage

### Custom Queries

Ask the PM specific questions:
- "What's the highest priority work for backend?"
- "Show me all security-related issues"
- "What's blocking the release?"
- "What did we accomplish this week?"

### Batch Operations

The PM can help with bulk updates:
- "Close all completed issues from last sprint"
- "Reprioritize all frontend work to P1"
- "Find all issues with TODO comments"

### Reporting

Request formatted reports:
- "Generate a standup report"
- "Show me priority distribution"
- "What percentage of work is tracked?"

## Troubleshooting

**PM seems too strict:**
- Good! That's the point. Strict discipline prevents chaos.
- If truly wrong, question the priority or dependency model.

**PM creates too many issues:**
- Every piece of work deserves tracking. It prevents forgotten work.
- Use labels and priorities to manage the volume.

**PM interrupts my flow:**
- Interruption prevents bigger problems. Take 30 seconds to file the issue.
- You can batch similar issues if in deep flow.

**TodoWrite and beads feel redundant:**
- They serve different purposes:
  - TodoWrite = session-level tactical task list
  - Beads = persistent project-level issue tracking
- PM keeps them aligned so you get benefits of both.

## Success Metrics

You're using the PM effectively when:
- ✅ Every commit references a beads issue
- ✅ TodoWrite perfectly mirrors beads priorities
- ✅ No untracked work exists
- ✅ Team always knows what to work on next
- ✅ Blockers are explicitly modeled
- ✅ Priority violations are rare and intentional

## See Also

- `AGENT_PM.md` - Complete PM agent specification
- `SKILL.md` - Full beads command reference
- `/beads:status` - Quick project dashboard
- `/beads:ready` - See ready work only
