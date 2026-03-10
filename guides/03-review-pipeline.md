# The Review Pipeline

## Why 6 Dimensions?

Code review is more than "does it look right?" Different aspects of code quality require different expertise. The review pipeline splits the review into 6 specialized dimensions, each handled by a dedicated agent:

| Dimension | Agent | What It Checks |
|-----------|-------|----------------|
| Architecture | `review-architecture` | Layer violations, dependency direction, module boundaries |
| Quality | `review-quality` | Readability, DRY, error handling, dead code |
| Tests | `review-tests` | Coverage, test quality, edge cases, conventions |
| Reuse | `review-reuse` | Duplicated code, missed utilities, inheritance |
| Efficiency | `review-efficiency` | N+1 queries, missing parallelization, redundant ops |
| Security | `review-security` | OWASP Top 10, injection, auth, secrets |

## How It Works

### Standalone Review (`/skill-review`)

1. Detects uncommitted changes via `git diff`
2. Spawns all 6 review agents **in parallel**
3. Each agent analyzes ONLY changed lines
4. Results are consolidated into a single report
5. Findings sorted by severity: BLOCKER > HIGH > MEDIUM > LOW

### As Part of Implementation (`/skill-implement`)

1. `coding-agent` implements the feature
2. All 6 review agents run in parallel
3. If BLOCKER/HIGH findings (except security):
   - `coding-agent` fixes them
   - Affected reviewers re-run
   - Max 3 iterations
4. Security findings presented to user (never auto-fixed)
5. Pipeline report generated

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| **BLOCKER** | Must fix before merging | Blocks the pipeline |
| **HIGH** | Should fix | Triggers fix-loop |
| **MEDIUM** | Consider fixing | Reported, not blocking |
| **LOW** | Suggestion | Reported, informational |

## The Fix Loop

```
Review Round 1 -> Findings (3 HIGH, 2 MEDIUM)
  |
coding-agent fixes 3 HIGH findings
  |
Review Round 2 -> Findings (1 HIGH, 2 MEDIUM)
  |
coding-agent fixes 1 HIGH finding
  |
Review Round 3 -> PASS (2 MEDIUM remaining, not blocking)
  |
Pipeline complete
```

### Deadlock Detection

If >80% of findings are identical after 2 fix rounds, the pipeline stops:
- The coding-agent is probably unable to resolve the issue
- The pipeline escalates to the user with the recurring findings
- User decides: fix manually, ignore, or adjust approach

## Security: Report-Only

Security findings are **NEVER automatically fixed**. Why?

1. **False positives** are common in security analysis
2. **Context matters** -- what looks like a vulnerability might be intentional
3. **Fixes can introduce new issues** -- security changes need human judgment
4. **Audit trail** -- security decisions should be deliberate and documented

Security findings are presented to the user with:
- Description of the potential vulnerability
- OWASP category reference
- Suggested remediation
- The user decides whether and how to fix

## Using Individual Reviews

If you do not need all 6 dimensions, use flags:

```
/skill-review --arch             # Architecture only
/skill-review --security         # Security only
/skill-review --tests --quality  # Multiple specific dimensions
```

## Customizing Reviews

Each review agent reads `.claude/rules/` for project-specific conventions. To customize what gets flagged:

1. Add rules to `.claude/rules/architecture.md`, `testing.md`, `security.md`
2. The review agents will check against your rules
3. No need to modify the agents themselves
