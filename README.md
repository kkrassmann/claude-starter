# Claude Code Starter Kit

## Why This Kit?

Claude Code is a CLI AI assistant that writes, reviews, and debugs code directly in your terminal. The key insight for productivity is **think in agents, not chats** -- delegate specialized work to focused agents instead of dumping everything into one conversation, keeping your main context sharp for decisions. This kit codifies battle-tested workflows into reusable skills and agents that you can adopt on day one.

## Quick Setup

### 1. Copy global configuration

Copy the contents of `global/` into your home directory:

```bash
cp global/CLAUDE.md ~/.claude/CLAUDE.md
cp global/settings.json ~/.claude/settings.json
```

### 2. Copy project configuration

Copy the contents of `project/` into your repository:

```bash
cp -r project/.claude <your-project>/.claude
```

### 3. Install plugins

```bash
claude mcp add github -- npx -y @anthropic-ai/claude-code-github-mcp
claude mcp add context7 -- npx -y @anthropic-ai/claude-code-context7-mcp
claude mcp add claude-mem -- npx -y @anthropic-ai/claude-code-memory-mcp
claude mcp add playwright -- npx -y @anthropic-ai/claude-code-playwright-mcp
```

### 4. Customize for your project

Launch Claude Code in your project directory and run:

```
/skill-adapt-starter
```

This skill analyzes your codebase (language, framework, test runner, architecture) and rewrites the template rules, agents, and skills to match your project.

## Recommended Plugins

| Plugin | Purpose | Priority |
|--------|---------|----------|
| **github** | Issues, PRs, code search, review comments directly from Claude | MUST |
| **context7** | Up-to-date library and framework documentation | MUST |
| **claude-mem** | Cross-session memory that persists between conversations | RECOMMENDED |
| **playwright** | Browser automation for testing and verification | OPTIONAL |

## The 3 Layers

Claude Code configuration works in three layers, from broadest to narrowest:

| Layer | Location | Scope | Example |
|-------|----------|-------|---------|
| **Global** | `~/.claude/CLAUDE.md` | Every project, every session | "Never kill processes without asking" |
| **Project** | `<project>/.claude/` | Everyone working on this repo | Architecture rules, test conventions, skills |
| **Session** | Conversation context | This conversation only | Current task, temporary state |

Global rules define how you want Claude to behave everywhere. Project rules define how your codebase works. Session context is what you are working on right now.

## Think in Agents, Not Chats

This is the single most important concept for productive AI-assisted development.

Your main conversation with Claude has a **context window** -- a fixed budget of text it can process. Every file you read, every test output you paste, every long code block consumes that budget. When it runs out, Claude starts forgetting earlier context and quality degrades.

**The solution: treat your main context as a dispatcher.** Keep it lean. Delegate heavy work to agents that run in their own separate context windows.

### Decision Tree

| Action | Where | Why |
|--------|-------|-----|
| Read a plan or ticket | Main context | Short, needed for coordination |
| Single git command | Main context | Trivial, minimal output |
| Small edit (<10 lines, 1-2 files) | Main context | Faster than spawning an agent |
| Targeted grep/glob search | Main context | Low output |
| Read files (>50 lines or >2 files) | **Agent** | Context killer #1 |
| Code edits (>3 files or >20 lines) | **Agent** | Keeps main context clean |
| Run tests | **Agent** | Agent runs tests and reports summary |
| Code review | **Agent** | Reads many files, produces focused report |
| Investigation or debugging | **Agent** | May need to read dozens of files |

### Bad vs. Good

**Bad -- everything in main context:**

```
You: "Read these 5 files, then implement the feature, then run tests"
  -> Claude reads 5 files (~800 lines into context)
  -> Claude makes 8 edits (more context consumed)
  -> Claude runs 3 test commands (~90 lines of output)
  -> Context is now 70% consumed for a single task
```

**Good -- dispatch to agents:**

```
You: "Implement the user notification feature per ticket #42"
  -> Claude spawns a coding-agent with the full task description
  -> Agent reads files, implements, runs tests in its OWN context
  -> Agent reports back: "Done. 4 files changed, 12 tests passing."
  -> Main context consumed: ~5 lines
```

The main context stays clean for the next task, the next decision, the next review.

## The 7 Skills

Skills are predefined workflows you invoke with `/skill-<name>`. They orchestrate agents, enforce quality gates, and encode best practices.

| Skill | What it does |
|-------|-------------|
| `/skill-implement` | Full implementation pipeline: code changes, then 6-dimension review, then fix-loop until clean |
| `/skill-coding` | Implement a feature or bugfix with escalation protocol (3 fix attempts, then debugger) |
| `/skill-review` | Run a 6-dimension code review: architecture, quality, security, testing, performance, documentation |
| `/skill-debug` | Bug hunting pipeline: reproduce, isolate, diagnose, fix, verify |
| `/skill-commit` | Create a commit with proper message format and issue reference |
| `/skill-run-tests` | Run tests and report a compact summary (passing/failing counts only) |
| `/skill-adapt-starter` | Analyze your project and customize all starter kit templates to match |

### Example: `/skill-implement`

```
You: /skill-implement "Add rate limiting to the /api/upload endpoint, max 10 requests per minute per user"
```

What happens behind the scenes:

1. A coding agent reads relevant files, implements the feature, writes tests, runs them
2. A review agent checks architecture, code quality, security, test coverage, performance, docs
3. If the review finds issues, a fix agent addresses them
4. Steps 2-3 repeat until the review is clean (max 3 cycles)
5. You get a summary: files changed, tests passed, review status

You stay in control without managing every step.

## Context Budget

Think of context like RAM. It is finite. Every interaction consumes some.

| Action | Context cost |
|--------|-------------|
| Reading a 200-line file | High |
| Running a test suite (full output) | Very high |
| A 3-line git command | Low |
| Agent summary ("12 passing, 0 failing") | Very low |

**Rules of thumb:**

- Never read whole files (>50 lines) in main context if you can delegate to an agent
- Never dump full test output into main context -- grep for `passing|failing` or let the agent summarize
- If context is above 70%, wrap up: save state, start a fresh session
- Parallel agents for independent tasks (e.g., reviewing module A while testing module B)

## Next Steps

Dive deeper into specific topics:

| Guide | What you will learn |
|-------|-------------------|
| [Workflow](guides/01-workflow.md) | Daily development workflow with Claude Code |
| [Skills and Agents](guides/02-skills-and-agents.md) | How to write your own skills and agents |
| [Review Pipeline](guides/03-review-pipeline.md) | The 6-dimension review system in detail |
| [Plugins](guides/04-plugins.md) | Plugin setup, configuration, and use cases |
| [Project Setup](guides/05-project-setup.md) | Adapting the starter kit to your project |
| [Context Management](guides/06-context-management.md) | Staying in control of the context window |

## Recommended Resources

| Repository | Description |
|------------|-------------|
| [better-ccflare](https://github.com/tombii/better-ccflare) | Improved Cloudflare integration for Claude Code |
| [awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code) | Curated list of great Claude Code projects, tools, and resources |
| [GitHub MCP](https://github.com/anthropics/claude-code-github-mcp) | Official MCP plugin for issues, PRs, code search, and reviews |
| [SQL MCP](https://github.com/anthropics/claude-code-sql-mcp) | Official MCP plugin for database queries and schema exploration |
| [Get Shit Done](https://github.com/gsd-build/get-shit-done/) | Opinionated project management framework for Claude Code -- roadmaps, phased execution, and verification built into your workflow |
| [PeonPing](https://www.peonping.com/) | 160+ Warcraft & StarCraft voice packs that yell at you when Claude is done. Never alt-tab to check your terminal again -- your Peon will let you know. Also doubles as a fitness coach, because apparently we need an Orc to remind us to do pushups. |

---

Built from real-world experience across production codebases. No theory -- only patterns that survived contact with deadlines.
