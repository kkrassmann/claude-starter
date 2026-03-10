# Project Setup Guide

How to configure Claude Code for a new project.

## Step 1: Copy Starter Files

```bash
# Copy project templates to your .claude/ directory
cp -r claude-starter/project/* <your-project>/.claude/

# Your project should now have:
# <your-project>/.claude/
#   agents/         (10 agent files)
#   skills/         (7 skill directories)
#   rules/          (3 template files)
#   CLAUDE.md.template
```

## Step 2: Run the Adaptation Skill

```bash
cd <your-project>
claude
# Then type: /skill-adapt-starter
```

This will:
1. Analyze your codebase (language, framework, test setup)
2. Ask a few questions about your architecture
3. Convert all `.template` files into real configuration
4. Create a `CLAUDE.md` in your project root

## Step 3: Review Generated Files

After adaptation, review these files:

| File | What to Check |
|------|---------------|
| `CLAUDE.md` (project root) | Build commands, architecture description |
| `.claude/rules/architecture.md` | Layer definitions, module structure |
| `.claude/rules/testing.md` | Test commands, conventions |
| `.claude/rules/security.md` | Auth patterns, validation rules |

## Step 4: Add Project-Specific Rules

The templates cover common patterns. Add rules specific to your project:

### Custom Architecture Rules

```markdown
# In .claude/rules/architecture.md, add:

## Project-Specific Rules
- All API responses must include a `requestId` field
- Database migrations must be reversible
- Feature flags must be used for incomplete features
```

### Custom Testing Rules

```markdown
# In .claude/rules/testing.md, add:

## Project-Specific Testing
- Integration tests must clean up test data (afterEach)
- Mock external APIs, never call them in tests
- Use factories for test data, not fixtures
```

## Step 5: Verify Setup

Test your setup by running:

```bash
# In your project directory, start Claude:
claude

# Try these commands:
/skill-review          # Should analyze your uncommitted changes
/skill-run-tests       # Should run your test suite
```

## Optional: Global Configuration

If you have not set up global settings yet:

```bash
# Copy global settings
cp claude-starter/global/CLAUDE.md ~/.claude/CLAUDE.md
cp claude-starter/global/settings.json ~/.claude/settings.json
```

## Optional: .claudeignore

Create a `.claudeignore` file in your project root to exclude files from Claude's context:

```
# Build artifacts
dist/
build/
node_modules/

# Large generated files
*.min.js
*.bundle.js
package-lock.json

# Binary files
*.png
*.jpg
*.pdf
```

## Directory Structure After Setup

```
<your-project>/
  CLAUDE.md                     # Project instructions (generated)
  .claudeignore                 # (optional) Files to exclude
  .claude/
    agents/                     # 10 agent definitions
      coding-agent.md
      implement-pipeline.md
      explorer.md
      debugger.md
      review-architecture.md
      review-quality.md
      review-tests.md
      review-reuse.md
      review-efficiency.md
      review-security.md
    skills/                     # 7 skills
      skill-implement/SKILL.md
      skill-coding/SKILL.md
      skill-review/SKILL.md
      skill-debug/SKILL.md
      skill-commit/SKILL.md
      skill-run-tests/SKILL.md
    rules/                      # Project rules
      architecture.md
      testing.md
      security.md
```

Note: `skill-adapt-starter` deletes itself after successful completion.
