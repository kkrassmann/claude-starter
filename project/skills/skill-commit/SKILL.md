---
description: "Commit changes with conventional format and optional issue reference"
user_invocable: true
---

# Commit

Create a git commit with a well-formatted message.

## Steps

1. **Review Changes**: Run `git diff --staged` and `git diff` to see all changes.

2. **Stage Files**: Stage relevant files (prefer specific files over `git add -A`).

3. **Draft Message**: Create a commit message:
   - Format: `type: description (#issue)`
   - Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`
   - If an issue number was mentioned, include it as `(#N)`
   - Keep the first line under 72 characters

4. **Confirm**: Show the draft message and ask the user to confirm before committing.

5. **Commit**: Create the commit.

## Examples
```
feat: Add user notification preferences (#42)
fix: Prevent duplicate entries in tag assignment (#15)
refactor: Extract shared validation logic
test: Add edge case tests for geofence boundary
```
