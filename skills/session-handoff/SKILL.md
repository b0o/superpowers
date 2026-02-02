---
name: session-handoff
description: "Use when approaching context limits (75-85%) to write comprehensive handoff documents for session continuity. Also use when continuing from a handoff document."
---

# Session Handoff

## Overview

Write comprehensive handoff documents when approaching context limits, and continue sessions from handoff documents.

**Core principle:** The next session has ZERO context. Write for a stranger.

**When to use:**
- Context usage reaches 75-85% and work remains
- User asks to pause and continue later
- Continuing a session from a handoff document

## CRITICAL: Understanding Context Loss

**The new session will have ZERO context from the current session.**

This is not like "pausing" - this is a complete reset. The next agent:
- Has no memory of this conversation
- Does not know what files you looked at
- Does not know what decisions you made or why
- Does not know what the user said
- Does not know what you learned about the codebase
- Only knows what is written in the handoff document

**If it's not in the handoff document, it does not exist for the next agent.**

You are not "handing off to yourself" - you are writing instructions for a stranger who happens to have the same capabilities. Everything you currently "just know" from this session MUST be explicitly written down or it is lost forever.

## Monitoring Context Usage

A hook injects context usage updates at 25%, 50%, 75%, then every 5% until 95%, then every 1% until 100%. You can also call the `checklimits` tool proactively.

At each context update, assess:
- How much work remains?
- Is there risk of running out before completing the task?
- Early updates (25%, 50%) usually don't have enough info to judge

## When to Wind Down

**Target: Start winddown at 80-90% context usage.**

- Not too early (waste context capacity)
- Not too late (need context for winddown steps, and model performance degrades after 90-95%)

**Trigger winddown when:**
- Context usage is 75-85% AND
- Remaining work likely exceeds remaining context AND
- You can reach a reasonable stopping point

## Writing a Handoff Document

### Step 1: Finish Current Atomic Task

Complete to a stopping point. Don't leave things half-done.

### Step 2: Write Handoff Document

You MUST use the template in `./handoff-template.md`. 

**MUST NOT:**
- Skip sections marked MUST
- Write brief summaries assuming the next agent "will figure it out"
- Use vague language like "continue implementing"

**MUST:**
- Fill every MUST section completely
- Be specific about file paths, line numbers, exact state
- Explain the "why" for every decision
- Capture user preferences and quirks explicitly

### Step 3: Save to Predictable Location

Save to: `docs/session-handoffs/YYYY-MM-DD-HH-MM-<topic>.md`

Create the directory if it doesn't exist.

### Step 4: Ask User to Continue

Use the question tool:

> "I'm approaching my context limit. I've written a detailed handoff document ready to continue in a new session. Ready to continue?"
> Options: Yes, continue in new session / Not yet, I have questions

### Step 5: Provide Continuation Prompt

```
I'm continuing a previous session. The handoff document is at:
docs/session-handoffs/[filename].md

Read that file and continue where the previous session left off.
```

The user will copy this into a new session.

**For deep work sessions, use this prompt instead:**
```
I'm continuing a previous deep work session. Use the deep-work-session skill to continue. The handoff document is at:
docs/session-handoffs/[filename].md

Read that file and continue the deep work session autonomously.
```

## Continuing From a Handoff Document

When the user provides a handoff file path to continue from:

### CRITICAL: You Have No Prior Context

**You are not "continuing" - you are starting fresh with a briefing document.**

You have no memory of the previous session. You do not know:
- What the user said before
- What was tried and failed
- What decisions were made or why
- What the user's preferences are
- What files are relevant

The handoff document is your ONLY source of context. You MUST read it completely and trust its contents.

### Step 1: Read Handoff Document

You MUST read the file completely before taking any action. It contains:
- Session preferences (if any were set)
- Task context and progress
- What was done, what remains
- Technical decisions and reasoning
- Open questions or blockers
- User preferences and quirks

### Step 2: Restore Session State

You MUST NOT re-ask preferences that are in the handoff document. Announce:

```
Continuing from [handoff file].

Restored context:
- [Key preference 1]
- [Key preference 2]

Progress: [X of Y tasks complete]
Resuming from: [current task]
```

### Step 3: Resume Work

- Pick up exactly where the previous session left off
- Follow the same preferences documented in the handoff
- Continue with the same working style

### Step 4: Handle Chained Handoffs

If this is a continuation of a continuation (chain of handoffs):
- The handoff document should reference prior handoffs if relevant
- Consolidate understanding, don't re-read entire chain
- Focus on current state and remaining work

## Red Flags

**MUST NOT:**
- Write a sparse handoff document - the next agent has ZERO context
- Assume the next agent "will figure it out" - it will not, it cannot
- Skip sections in the handoff template - every MUST section is required
- Write vague next steps like "continue implementing" - be specific
- Re-ask preferences when continuing from a handoff - trust the document
- Ignore context usage warnings until it's too late to write a proper handoff

**MUST:**
- Write handoff documents as if explaining to a stranger (because you are)
- Include the "why" for every technical decision, not just the "what"
- Capture user preferences and quirks explicitly
- Monitor context usage and plan winddown at 80-90%
- Use `checklimits` tool proactively if unsure about remaining context

## Integration

**Used by:**
- `deep-work-session` - for handling context limits in autonomous sessions
- Any long-running session approaching context limits

**Works with:**
- All skills that establish session preferences (those should be captured in handoff)
