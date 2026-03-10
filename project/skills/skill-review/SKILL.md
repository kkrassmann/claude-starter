---
description: "6-dimension code review: architecture, quality, tests, reuse, efficiency, security"
user_invocable: true
---

# Code Review

Run code reviews on uncommitted changes.

## Usage

- `/skill-review` -- Run all 6 review dimensions in parallel
- `/skill-review --arch` -- Architecture only
- `/skill-review --quality` -- Quality only
- `/skill-review --tests` -- Tests only
- `/skill-review --reuse` -- Reuse only
- `/skill-review --efficiency` -- Efficiency only
- `/skill-review --security` -- Security only

## Steps

1. **Check for changes**: Run `git diff` to identify uncommitted changes. If no changes, inform the user.

2. **Determine scope**: Check if a specific flag was provided or run all.

3. **Run reviews**: Spawn the selected review agent(s). If running all, spawn all 6 in parallel:
   - `review-architecture`
   - `review-quality`
   - `review-tests`
   - `review-reuse`
   - `review-efficiency`
   - `review-security`

4. **Report**: Present findings from each dimension. Group by severity (BLOCKER > HIGH > MEDIUM > LOW).

## Severity Guide
- **BLOCKER**: Must fix before merging (security holes, data loss risks)
- **HIGH**: Should fix (architecture violations, missing tests for critical paths)
- **MEDIUM**: Consider fixing (code smells, minor inefficiencies)
- **LOW**: Suggestions (style, optional improvements)
