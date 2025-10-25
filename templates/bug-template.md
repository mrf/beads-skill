# Bug Report Template

Use this as a guide when filing bugs with Beads.

## Required Information

```bash
bd create "Bug: [Short description]" \
    -t bug \
    -p [0-2] \
    -l "bug,[area]" \
    -d "
## Description
[What is broken?]

## Steps to Reproduce
1. [First step]
2. [Second step]
3. [etc.]

## Expected Behavior
[What should happen?]

## Actual Behavior
[What actually happens?]

## Environment
- Version: [version]
- OS: [operating system]
- [other relevant details]

## Location
[File and line number if known]

## Additional Context
[Screenshots, logs, etc.]
"
```

## Priority Guidelines

- **0 (Critical)**: System down, data loss, security vulnerability
- **1 (High)**: Major functionality broken, workaround exists
- **2 (Medium)**: Minor functionality issue, cosmetic bug
- **3 (Low)**: Nice-to-have fix, minimal impact
- **4 (Backlog)**: Future consideration

## Common Labels

- `bug` - Always include for bugs
- `security` - Security-related issues
- `performance` - Performance problems
- `ui` - User interface bugs
- `backend` - Backend/API bugs
- `frontend` - Frontend bugs
- `database` - Database-related issues
- `regression` - Previously working functionality
- `critical` - Critical severity

## Example

```bash
bd create "Bug: Login fails with special characters in password" \
    -t bug \
    -p 1 \
    -l "bug,security,backend" \
    -d "
## Description
Users cannot login when their password contains special characters like @, #, or %.

## Steps to Reproduce
1. Create account with password containing @ symbol
2. Attempt to login with those credentials
3. Login fails with 'Invalid credentials' error

## Expected Behavior
Login should succeed with correct credentials regardless of special characters.

## Actual Behavior
Login fails. Server logs show SQL injection protection triggering false positive.

## Environment
- Version: 1.2.3
- OS: Ubuntu 22.04
- Database: PostgreSQL 14

## Location
auth/handler.go:142

## Additional Context
Affects ~5% of users based on error logs.
"
```
