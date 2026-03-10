# Context Window Management

The context window is your most valuable resource when working with Claude Code. Every file you read, every test output, every conversation turn — it all eats into a fixed budget. When it fills up, Claude compacts your conversation and details get lost.

This guide teaches you how to stay in control.

## The Problem: Compaction

When your conversation approaches the context limit, Claude automatically compresses older messages. This is called **auto-compaction**. Here is what happens:

- Detailed code snippets get summarized into one-liners
- Multi-step plans lose intermediate steps
- File contents you read earlier disappear
- Agent results get compressed to summaries of summaries

The result: Claude "forgets" parts of the plan, re-reads files it already saw, makes decisions that contradict earlier ones, or asks you questions you already answered. You end up spending more time recovering context than doing actual work.

## Plan-Driven Development

The single most important technique: **always have a plan file**.

A plan file is your external memory. When compaction happens, the plan survives on disk. Claude can re-read it. Without a plan, everything lives only in the context window — and once compacted, it is gone.

```
# Bad: Everything in your head
You: "Add authentication to the API"
Claude: reads 8 files, designs approach, starts implementing...
[compaction happens]
Claude: "What were we doing again? Let me re-read those files..."

# Good: Plan-driven
You: "Add authentication to the API"
Claude: writes plan to .claude/plans/auth.md
Claude: implements step by step, checking plan as reference
[compaction happens]
Claude: reads plan file, continues exactly where it left off
```

Use `/plan` or ask Claude to enter plan mode before any non-trivial task. The plan file costs almost nothing — but saves everything.

## The Context Budget

Not all operations cost the same. Here is a rough breakdown:

| Operation | Context Cost | Alternative |
|-----------|-------------|-------------|
| Read a 200-line file | High | Agent reads it, returns summary |
| Full test output (100+ lines) | Very high | Agent runs tests, returns pass/fail |
| Read a 20-line function | Low | Fine in main context |
| Agent result (summary) | Low | This is the way |
| A single edit | Low | Fine in main context |
| Conversation back-and-forth | Medium | Be specific, reduce iterations |

**Decision tree: Main context or agent?**

```
Is it a small, targeted operation? (<50 lines, 1-2 files)
  → Main context

Is it reading, searching, or analyzing code?
  → Agent (Explore or general-purpose)

Is it implementing changes across multiple files?
  → Agent (coding)

Is it running tests?
  → Agent (return summary only)

Are you unsure?
  → Agent. The overhead is small, the savings are large.
```

## Staying Under the Limit

### 1. Plans as External Memory

Write plans to disk. Reference them instead of keeping everything in conversation:

```
You: "Read the plan at .claude/plans/feature.md and continue with step 3"
```

Claude reads the file, gets full context, and continues. Much cheaper than having the entire plan history in conversation.

### 2. Delegate to Agents

Agents run in their own context window. Their results come back as compact summaries. This is the biggest lever you have:

```
# Bad: 500 lines of test output in your context
You: "Run the tests"

# Good: 2 lines of summary
You: "Run the tests using an agent, return only pass/fail summary"
```

### 3. Never Read Large Files in Main Context

If you need to understand a file, send an agent. If you need to edit a specific function, read only that function (use line ranges). The worst thing you can do is `Read` a 500-line file "just to see what is in there."

### 4. Keep Conversations Focused

One task per session. If you finish a feature and want to start a new one, consider starting a fresh session. The old context is dead weight for the new task.

### 5. Watch the Context Indicator

Claude Code shows your context usage. When you hit ~70%, it is time to either:
- Wrap up the current task
- Start a new session with a handoff
- Save your plan and continue fresh

## Recovery: When Compaction Happens

Sometimes it happens despite your best efforts. Here is what to do:

### Option 1: Re-read the Plan

If you have a plan file, just ask Claude to read it:

```
You: "Read .claude/plans/feature.md and continue where we left off"
```

### Option 2: Start a New Session

For major compaction, a new session is often cleaner:

```
You: "Summarize what we have done so far and what remains. Write it to .claude/plans/handoff.md"
# Start new session
You: "Read .claude/plans/handoff.md and continue the implementation"
```

### Option 3: Use Memory

If you use `claude-mem` or similar memory tools, key decisions and patterns survive across sessions automatically. Save important architectural decisions to memory as you go.

## Real-World Examples

### Example 1: The Context Explosion

> You ask Claude to add a payment system. Claude reads 12 files to understand the codebase, writes 200 lines of implementation, runs tests (80 lines of output), hits an error, reads 3 more files to debug...
>
> Context is at 90%. Compaction fires. Claude forgets half the implementation plan and starts re-reading files it already saw. You spend 20 minutes getting back to where you were.

### Example 2: The Disciplined Approach

> You ask Claude to plan the payment system. Claude writes a plan with 6 steps. You approve it.
>
> Claude dispatches a coding agent for step 1. Agent returns: "Created PaymentService with 3 methods, all tests passing." You move to step 2. Each step is a clean agent call.
>
> Context stays at 30% throughout. No compaction. No lost work.

### Example 3: Mid-Session Recovery

> You are at step 4 of 6 when you notice context is getting high. You ask Claude to update the plan file with current progress. Then you start a new session:
>
> "Read .claude/plans/payment.md and continue with step 5."
>
> Clean context, full plan, no lost work. The session split took 30 seconds.

## Quick Reference

| Do | Don't |
|----|-------|
| Write a plan before implementing | Jump straight into coding complex features |
| Delegate file reads to agents | Read large files in main context |
| Get test summaries from agents | Dump full test output into context |
| One task per session | Chain unrelated tasks in one session |
| Save progress to plan files | Keep everything in conversation memory |
| Start fresh at 70% context | Push through until compaction hits |
| Use memory tools for key decisions | Rely on conversation history alone |
| Be specific in your requests | Have long back-and-forth clarifications |
