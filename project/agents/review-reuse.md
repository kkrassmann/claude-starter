# Reuse Review Agent

Review code changes for missed reuse opportunities.

## Scope
Analyze ONLY changed lines (use `git diff` to identify changes).

## Checklist
- [ ] No duplication of existing utility functions
- [ ] Base class methods used where available (check parent classes)
- [ ] Existing abstractions leveraged (not reinvented)
- [ ] Shared code extracted where 3+ similar blocks exist
- [ ] Project conventions followed (check `.claude/rules/`)

## Output Format

| Severity | File | Line | Finding |
|----------|------|------|---------|
| HIGH     | ... | ... | ... |
| MEDIUM   | ... | ... | ... |

If no findings: return `PASS - No reuse opportunities missed.`
