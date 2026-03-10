---
description: "Customize starter kit templates for your project (one-time setup)"
user_invocable: true
---

# Adapt Starter Kit

Interactive project adaptation. Analyzes your codebase and customizes the starter kit templates.

**Run this once after copying the `project/` directory to your `.claude/` folder.**

## Steps

### Phase 1: Explore
Spawn an `explorer` agent to analyze the codebase:
- Language and framework (Node.js, Python, Java, Go, etc.)
- Architecture pattern (MVC, layered, microservices, monolith)
- Test framework (mocha, jest, pytest, junit, etc.)
- Package manager (npm, pip, maven, gradle)
- Key directories (src/, test/, lib/, app/)
- Existing `.claude/` configuration or `CLAUDE.md`

### Phase 2: Interactive Q&A
Use AskUserQuestion for things that could not be auto-detected:
- "What are the main architectural layers in your project?" (pre-fill if detected)
- "How do you run tests?" (pre-fill with detected command)
- "Do you use a specific branching strategy?" (e.g., trunk-based, gitflow)
- "Are there critical security patterns to enforce?" (e.g., input validation, auth checks)
- Skip questions where auto-detection was confident

### Phase 3: Adapt
Based on exploration + answers, modify all template files:

1. **Rules**: Convert templates to final files:
   - `rules/architecture.md.template` -> `rules/architecture.md`
     - Replace `<!-- CUSTOMIZE: layers -->` with detected/provided layers
     - Replace `<!-- CUSTOMIZE: module-structure -->` with actual structure
   - `rules/testing.md.template` -> `rules/testing.md`
     - Replace `<!-- CUSTOMIZE: test-command -->` with detected command
     - Replace `<!-- CUSTOMIZE: test-framework -->` with detected framework
   - `rules/security.md.template` -> `rules/security.md`
     - Adapt to framework-specific security patterns

2. **Project CLAUDE.md**:
   - If `CLAUDE.md` exists in project root: offer to MERGE (not overwrite)
   - If not: convert `CLAUDE.md.template` -> `../CLAUDE.md` (project root)
   - Fill in real paths, commands, and architecture description

3. **Skills**:
   - Update `skill-run-tests/SKILL.md` with detected test command

4. **Agents**:
   - Update `coding-agent.md` with detected architecture pattern

5. **Cleanup**: Delete all `.template` files after successful adaptation

### Phase 4: Summary
Show what was adapted:
- List of files created/modified
- Items that need manual review
- Suggest next steps (try `/skill-review` on existing code)

## Design Principles
- Auto-detect first, ask only when uncertain
- Pre-fill answers where possible
- Merge, do not overwrite existing config
- This skill deletes itself after successful completion (one-time use)
