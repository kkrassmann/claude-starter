---
description: "Full implementation pipeline: code + 6 reviews + fix-loop + commit offer"
user_invocable: true
---

# Implementation Pipeline

Run the full implementation pipeline for the given task.

## Steps

1. **Implement**: Spawn a `coding-agent` (subagent_type="coding-agent") with the task.
   - If it returns `STATUS: NEEDS_DEBUGGER`, spawn `debugger` agent, feed diagnosis back to a new coding-agent.

2. **Review**: Spawn ALL 6 review agents **in parallel** (subagent_type for each):
   - `review-architecture`
   - `review-quality`
   - `review-tests`
   - `review-reuse`
   - `review-efficiency`
   - `review-security`

3. **Fix Loop**: If BLOCKER or HIGH findings exist (except security):
   - Spawn `coding-agent` to fix findings
   - Re-run affected review agents
   - Max 3 iterations. If >80% findings identical after 2 rounds, escalate to user.

4. **Security**: Present security findings to user (NEVER auto-fix).

5. **Report**: Show pipeline summary (status per review dimension).

6. **Commit**: If all reviews pass, offer to commit using `/skill-commit`.
