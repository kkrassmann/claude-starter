---
description: "Bug hunting: parallel exploration + diagnosis + fix offer"
user_invocable: true
---

# Debug

Systematic bug hunting pipeline.

## Steps

1. **Gather Context**: Spawn two agents in parallel:
   - `explorer` agent: Search codebase for relevant code paths
   - `debugger` agent: Analyze error logs, stack traces, and runtime behavior

2. **Diagnose**: Combine findings from both agents to identify root cause.

3. **Present Diagnosis**: Show the user:
   - Root cause description
   - Evidence (logs, code references)
   - Affected files
   - Confidence level (HIGH/MEDIUM/LOW)

4. **Offer Fix**: Ask the user if they want to fix the issue.
   - If yes: Spawn `coding-agent` with the diagnosis
   - If no: End with the diagnosis report

## Input
Provide one of:
- Error message or stack trace
- Failing test name
- Bug description (what happens vs what should happen)

## Tips
- Include the error log or stack trace in your request
- Mention which action triggers the bug
- Specify if it is reproducible or intermittent
