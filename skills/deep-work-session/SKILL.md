---
name: deep-work-session
description: "Use when user explicitly mentions 'deep work' or signals substantial work ('let's build/implement X'). Sets session preferences upfront to enable autonomous execution. If uncertain, ask: 'Deep Work session or interactive style?'"
---

# Deep Work Session

## Overview

Set up a focused working session with preferences established upfront, enabling autonomous execution with minimal interruption.

**Core principle:** Front-load decisions so the agent can work autonomously.

**When to use:**
- User explicitly mentions "deep work" → definite trigger
- User signals substantial work ("let's build X", "let's implement X") → ask first

**When NOT to use:**
- Simple questions or quick fixes
- User dives directly into a specific task without signaling session intent
- Information requests

## Trigger Behavior

```dot
digraph trigger {
    "User message" [shape=doublecircle];
    "Mentions 'deep work'?" [shape=diamond];
    "Start deep work session" [shape=box];
    "Signals substantial work?" [shape=diamond];
    "Ask: Deep Work or interactive?" [shape=box];
    "Proceed normally" [shape=box];
    
    "User message" -> "Mentions 'deep work'?";
    "Mentions 'deep work'?" -> "Start deep work session" [label="yes"];
    "Mentions 'deep work'?" -> "Signals substantial work?" [label="no"];
    "Signals substantial work?" -> "Ask: Deep Work or interactive?" [label="yes"];
    "Signals substantial work?" -> "Proceed normally" [label="no"];
}
```

**Substantial work signals:**
- "Let's build/implement/create X"
- "I want to work on X"
- Multi-step requests
- Feature development, refactoring, complex fixes

**Use the question tool to ask:**
> "Would you like to start a Deep Work session (autonomous execution with upfront planning) or work interactively?"
> Options: Deep Work session, Interactive style

## Session Setup

**Announce:** "Starting Deep Work session. Let me gather a few preferences upfront."

### Step 1: Analyze Task and Present Defaults

Analyze what the user wants to do and present a proposed configuration:

```
Deep Work session for: [task description]

Proposed configuration:
- Commits: Auto-commit after each task
- Questions: Front-loaded (ask everything now, then work autonomously)
- Scope: [inferred - e.g., "src/components/", "no breaking changes"]
- Skills: [relevant skills - e.g., "TDD, verification-before-completion"]

Which would you like to change?
```

**Use the question tool with multiple selection:**
- Commit preference
- Question style  
- Scope boundaries
- Skills to use
- None - looks good

### Step 2: Gather Changes (if any)

Only ask follow-up questions for items the user selected to change.

**Commit options:**
- Auto-commit after each task (default)
- Ask before each commit
- Batch commits at end
- No commits (user handles)

**Question style options:**
- Front-loaded then minimal (default) - ask foreseeable questions now, then only interrupt if truly blocked
- Minimal - proceed unless blocked, no upfront gathering
- Checkpoint - pause at major decision points
- Collaborative - frequent check-ins

**Scope options** (ask whichever are relevant):
- Files/directories to focus on or avoid
- Change magnitude (small fix, refactor, rewrite)
- Testing requirements
- Time/effort budget

**Skills options:**
- Present skills that seem relevant to the task
- Let user confirm or decline each

### Step 3: Plan the Workflow

Based on the task, propose a sequence of skills:

```
Proposed workflow:
1. brainstorming - explore the design
2. writing-plans - create implementation plan
3. subagent-driven-development - execute with fresh agents per task

Does this workflow look right?
```

**Use the question tool:**
- Yes, proceed
- Adjust workflow (let me specify)

**Common workflows:**

| Task Type | Typical Workflow |
|-----------|------------------|
| New feature (unclear scope) | brainstorming → writing-plans → subagent-driven-development |
| New feature (clear spec) | writing-plans → subagent-driven-development |
| Bug fix | systematic-debugging → TDD fix → verification |
| Refactoring | writing-plans → executing-plans |
| Exploration | brainstorming only |

### Step 4: Confirm and Hand Off

Summarize the session configuration:

```
Deep Work session configured:
- Commits: Auto-commit
- Questions: Front-loaded then minimal
- Scope: [summary]
- Workflow: brainstorming → writing-plans → subagent-driven-development

Starting with brainstorming...
```

## Context Propagation

When invoking downstream skills, pass the session context:

**For all skills:**
```
[DEEP WORK SESSION]
- Commit preference: [auto/ask/batch/none]
- Question style: [front-loaded/minimal/checkpoint/collaborative]
- Scope: [boundaries]

Operate with maximum autonomy. User has answered foreseeable questions.
If question style is "front-loaded then minimal": only interrupt if truly blocked.
```

**For brainstorming specifically:**
- Brainstorming remains interactive (that's its nature)
- But focus on gathering everything needed for autonomous execution
- At the end, explicitly ask: "Is there anything else I should know before proceeding autonomously?"

## Session Preferences Reference

These preferences persist for the entire session. Other skills should check if they're already set before asking.

| Preference | Key | Values |
|------------|-----|--------|
| Commits | `COMMIT_PREFERENCE` | `auto`, `ask`, `batch`, `none` |
| Questions | `QUESTION_STYLE` | `front-loaded`, `minimal`, `checkpoint`, `collaborative` |
| Scope | `SCOPE_BOUNDARIES` | Free text |
| Skills | `SKILL_WORKFLOW` | Ordered list |

## Context Management

Deep work sessions may exceed the context window.

**REQUIRED SUB-SKILL:** Use superpowers:session-handoff for all context management.

When approaching context limits (75-85%), the session-handoff skill handles:
- Writing comprehensive handoff documents
- Guiding the user to continue in a new session
- Ensuring no context is lost

**Key points for deep work:**
- Target winddown at 80-90% context usage
- Include all deep work preferences in the handoff document
- Use the deep work continuation prompt from session-handoff

## Continuing a Deep Work Session

When the user says they're continuing a deep work session and provides a handoff file path:

**REQUIRED SUB-SKILL:** Use superpowers:session-handoff to read and restore from the handoff document.

After restoring context from the handoff:

1. **Verify deep work preferences are restored** - commits, question style, scope, workflow
2. **Announce the restored configuration:**

```
Continuing Deep Work session from [handoff file].

Restored configuration:
- Commits: [from handoff]
- Questions: [from handoff]  
- Scope: [from handoff]
- Workflow: [from handoff]

Progress: [X of Y tasks complete]
Resuming from: [current task]
```

3. **Resume autonomously** - preferences already gathered, continue with maximum autonomy

## Red Flags

**MUST NOT:**
- Re-ask preferences when continuing from a handoff - trust the document
- Start deep work session for simple questions
- Skip preference gathering ("I'll just use defaults")
- Forget to pass session context to downstream skills
- Interrupt during autonomous work unless truly blocked
- Ask questions that were already answered in setup

**MUST:**
- Ask "Deep Work or interactive?" when uncertain about user intent
- Use session-handoff skill when approaching context limits
- Monitor context usage and plan winddown at 80-90%

**SHOULD:**
- Present smart defaults based on task analysis
- Propose workflow and let user confirm
- Pass full session context to every skill invoked
- Trust the front-loaded decisions during execution
- Use `checklimits` tool proactively if unsure about remaining context

## Integration

**Required sub-skills:**
- `session-handoff` - for context management and session continuity

**Skills that should check for existing session preferences:**
- `writing-plans` - check `COMMIT_PREFERENCE` and `QUESTION_STYLE` before asking
- `subagent-driven-development` - check `COMMIT_PREFERENCE` and `QUESTION_STYLE` before asking
- `brainstorming` - adjust verbosity based on `QUESTION_STYLE`
- `executing-plans` - respect `COMMIT_PREFERENCE` and `QUESTION_STYLE`

**Skills this typically hands off to:**
- `brainstorming` - for unclear scope
- `writing-plans` - for clear spec
- `systematic-debugging` - for bug fixes
- `subagent-driven-development` or `executing-plans` - for implementation
