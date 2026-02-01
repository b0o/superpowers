---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## Question Style (Check First)

**Before starting, check if a question style was set this session** (e.g., by deep-work-session).

| Question Style | Batch Checkpoint Behavior |
|----------------|---------------------------|
| `front-loaded` | Skip checkpoints. Continue autonomously. Only stop if truly blocked. |
| `minimal` | Skip checkpoints. Continue autonomously. Only stop if truly blocked. |
| `checkpoint` | Pause at batch boundaries for feedback (default behavior). |
| `collaborative` | Pause at batch boundaries for feedback. |
| Not set | Pause at batch boundaries for feedback (default behavior). |

**If front-loaded or minimal:** Execute all tasks continuously. Report progress but don't wait for feedback between batches. Only use the question tool if genuinely blocked.

## The Process

### Step 1: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch
**Default batch size: 3 tasks** (but execute all continuously if question style is front-loaded/minimal)

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed

### Step 3: Report (or Continue)

**If question style is front-loaded or minimal:**
- Log progress briefly but DO NOT stop for feedback
- Continue immediately to next batch
- Only stop if genuinely blocked

**Otherwise (checkpoint, collaborative, or not set):**
- Show what was implemented
- Show verification output
- Say: "Ready for feedback."
- Wait for user input before continuing

### Step 4: Continue
Based on feedback (if you waited for it):
- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 5: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly

**Use the question tool to ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and use the question tool to ask.

## Remember
- Review plan critically first
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- **Respect question style:** If front-loaded/minimal, continue autonomously without asking for feedback between batches
- If question style allows checkpoints: report and wait between batches
- Stop when blocked, don't guess
- Never start implementation on main/master branch without explicit user consent

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
