---
description: "Implement a feature or fix a bug with coding-agent + debugger escalation"
user_invocable: true
---

# Coding

Implement the given task using a coding-agent with debugger escalation.

## Steps

1. Spawn a `coding-agent` with the task description.

2. If the coding-agent returns `STATUS: NEEDS_DEBUGGER`:
   - Spawn the `debugger` agent with the error context
   - Feed the diagnosis back to a new `coding-agent`

3. Report the result:
   - What was changed
   - Test results
   - Any open concerns

## When to use
- Single feature or bug fix
- When you want implementation without full review pipeline
- Use `/skill-implement` instead if you want automatic reviews
