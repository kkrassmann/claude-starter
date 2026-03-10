# Skills and Agents

## What's the Difference?

| | Skills | Agents |
|---|--------|--------|
| **Invoked by** | User (slash command) | Claude (programmatically) |
| **Purpose** | Orchestrate workflows | Execute specific tasks |
| **Runs in** | Main context | Separate context (subprocess) |
| **Format** | `SKILL.md` with frontmatter | Markdown file in `.claude/agents/` |
| **Example** | `/skill-implement` | `coding-agent` |

**Skills** are user-facing workflows. They orchestrate one or more agents.
**Agents** are worker units. They do the actual work in isolated contexts.

## Skill Format (SKILL.md)

Skills live in `.claude/skills/<skill-name>/SKILL.md`:

```yaml
---
description: "Short description shown in skill list"
user_invocable: true
---

# Skill Name

Instructions for what Claude should do when this skill is invoked.

## Steps
1. First step
2. Second step
```

Key rules:
- `description` is shown when users type `/` to see available skills
- `user_invocable: true` makes it appear in the slash command list
- Instructions can reference agents by name (they will be spawned as subagents)
- Skills run in the main context, so keep instructions concise

## Agent Format

Agents live in `.claude/agents/<agent-name>.md`:

```markdown
# Agent Name

You are a [role description]. You [what you do].

## Workflow
1. Step one
2. Step two

## Rules
- Rule one
- Rule two

## Output Format
Report: [what to return]
```

Key rules:
- Agents run in separate contexts (they do not see your main conversation)
- Give them ALL context they need in the spawn prompt
- Define clear output formats so results are easy to parse
- Keep agents focused on ONE responsibility

## Writing Custom Skills

### Example: A Deployment Skill

```yaml
---
description: "Deploy to staging environment"
user_invocable: true
---

# Deploy to Staging

## Steps
1. Run `git status` to check for uncommitted changes. If dirty, ask user to commit first.
2. Run tests: spawn coding-agent to run the test suite.
3. If tests pass: run `./deploy.sh staging`
4. Verify deployment: check health endpoint
5. Report: deployed version, health status, URL
```

### Example: A Documentation Skill

```yaml
---
description: "Generate API documentation from code"
user_invocable: true
---

# Generate API Docs

## Steps
1. Spawn explorer agent: "Find all route definitions and their handlers"
2. For each route: extract method, path, parameters, response format
3. Generate markdown documentation
4. Write to docs/api.md
```

## Writing Custom Agents

### Example: A Migration Agent

```markdown
# Database Migration Agent

You create and verify database migrations.

## Workflow
1. Read the requested schema change
2. Check current schema (read migration files or query DB)
3. Generate migration file following project conventions
4. Generate rollback migration
5. Verify: dry-run the migration if possible

## Rules
- Always create rollback migrations
- Never drop columns without asking
- Use the project's migration framework
```

## Best Practices

1. **Skills orchestrate, agents execute** -- do not put implementation details in skills
2. **One agent, one job** -- keep agents focused
3. **Clear output contracts** -- define what agents return
4. **Fail gracefully** -- agents should report errors, not crash silently
5. **Context-aware** -- agents should check `.claude/rules/` for project conventions
