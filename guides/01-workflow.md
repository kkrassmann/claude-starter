# Workflow: Think in Agents, Not Chats

This is the most important concept for productive AI-assisted development.

## The Core Idea

Claude Code has a **context window** -- a limited amount of information it can hold in a single conversation. Every file you read, every test output, every error log consumes part of this budget. When it fills up, older context gets compressed and details are lost.

**Agents solve this.** Each agent runs in its own context. When it finishes, only its compact result comes back to your main conversation. This means:

- Your main context stays clean for **decisions and coordination**
- Heavy work (file reading, implementation, testing) happens in agent contexts
- You can run multiple agents **in parallel** for independent tasks

## Decision Tree: Main Context vs Agent

| Task | Where? | Why |
|------|--------|-----|
| Read a plan or short config | Main context | Small, needed for coordination |
| Git commands (status, checkout) | Main context | Trivial, minimal output |
| Read files (>50 lines or >2 files) | **Agent** | Context killer #1 |
| Implement code (>3 files or >20 lines) | **coding-agent** | Keeps main context clean |
| Run tests | **coding-agent** | Agent runs + reports summary only |
| Small edits (<10 lines, 1-2 files) | Main context | Faster than spawning agent |
| Targeted search (specific file/function) | Main context (Grep/Glob) | Low output |
| Broad codebase exploration | **explorer agent** | Could return a lot of data |

## Anti-Pattern: The Context Killer Session

Bad workflow:
1. Read 5 source files in main context (~800 lines consumed)
2. Make 8 edits in main context
3. Run tests 3 times with full output (~30 lines each)
4. Context is 70% full, Claude starts forgetting earlier decisions

**Result:** Claude loses track of what it already fixed, re-introduces bugs, asks questions you already answered.

## Good Pattern: The Dispatcher Session

Good workflow:
1. Describe the task in main context
2. Spawn `coding-agent` with full task description
3. Agent reads files, implements, runs tests, reports back
4. You get a 10-line summary: "Changed 3 files, 45 tests passing"
5. Main context used: ~5%

## Practical Workflow Examples

### Implementing a Feature

```
You: "Add email validation to the registration endpoint"

Main context actions:
1. Spawn coding-agent: "Add email validation to registration. Check existing
   validation patterns first. Run tests after."
2. Read agent result (10-line summary)
3. If tests pass: "/skill-commit"
4. If tests fail: spawn another coding-agent with the error
```

### Investigating a Bug

```
You: "Users report 500 error on /api/users/profile"

Main context actions:
1. Spawn explorer + debugger in parallel:
   - Explorer: "Find the profile endpoint handler and its dependencies"
   - Debugger: "Check error.log for recent 500 errors on /api/users/profile"
2. Read both results (~20 lines total)
3. You now understand the issue without reading a single file yourself
4. Spawn coding-agent with the fix instructions
```

### Reviewing Code

```
You: "/skill-review"

Main context actions:
1. Skill spawns 6 review agents in parallel
2. Each returns findings (or PASS)
3. You see a consolidated report
4. Decide which findings to fix
```

## Plan Mode

For complex tasks (multi-file features, unclear requirements, large refactoring), use **Plan Mode**:

1. Type `/plan` or ask Claude to enter plan mode
2. Claude creates a structured plan without making changes
3. You review and adjust the plan
4. Exit plan mode and execute (or delegate to `/skill-implement`)

**When to use plan mode:**
- New feature spanning multiple files
- Refactoring with unclear scope
- When you are not sure about the approach

**When NOT to use plan mode:**
- Simple bug fix (just fix it)
- Single-file change (just do it)
- You already know exactly what to do

## Context Recovery

If your context gets too full (Claude starts forgetting things):
1. Save your current state (what is done, what is left)
2. Start a new conversation
3. Describe where you left off
4. Continue with agents

Cross-session memory (claude-mem plugin) helps here -- it persists knowledge across conversations.

## Summary

| Principle | What It Means |
|-----------|---------------|
| Main context = dispatcher | Only coordination, decisions, small edits |
| Agents = workers | File reading, implementation, testing |
| Parallel when possible | Independent tasks run simultaneously |
| Plan mode for complexity | Think first, execute second |
| Never read large files in main | Always delegate to an agent |
| Test output = summary only | Grep for pass/fail, not full logs |

The single most impactful habit: **before doing something in main context, ask yourself if an agent should do it instead.** The answer is yes more often than you think.
