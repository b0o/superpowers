---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

## Commit Preference (Check or Ask)

**First, check if a commit preference was already set this session** (e.g., by deep-work-session).

**If already set:** Use that preference. Don't ask again.

**If not set, use the question tool to ask:**
> "Should commits be included as steps in this plan?"
> Options: Yes, No

**If No:**
- Remove all commit steps from the plan
- Replace "frequent commits" guidance with "frequent saves/checkpoints"
- This preference applies for the **entire session**, including any future skill invocations
- The agent MUST NOT commit unless the user **explicitly gives permission** later

**If Yes (or user doesn't specify):**
- Include commit steps as normal
- Follow standard "frequent commits" guidance

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step (only if user opted in to commits)

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

```markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit** *(only if user opted in to commits)*

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD
- Respect user's commit preference (session-wide)

## Execution Handoff

After saving the plan, check the session's question style before asking about execution.

### If Question Style is Front-Loaded or Minimal (Deep Work Session)

**Do NOT ask about execution method.** Proceed directly:

- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session, execute autonomously
- The user has already committed to autonomous execution by choosing deep work

Just announce: "Plan saved. Proceeding with subagent-driven execution."

### Otherwise (Interactive Mode or No Session Context)

Use the question tool to offer execution choice:

**Question:** "Plan complete and saved. How would you like to execute it?"

**Options:**
1. **Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration
2. **Parallel Session (separate)** - Open new session with executing-plans, batch execution with checkpoints

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:subagent-driven-development
- Stay in this session
- Fresh subagent per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses superpowers:executing-plans
