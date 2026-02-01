# Deep Work Session Handoff Template

Use this template when writing a handoff document for session continuation.

Save to: `docs/deep-work-handoffs/YYYY-MM-DD-HH-MM-<topic>.md`

---

## CRITICAL: The Next Session Has ZERO Context

**The agent reading this document has NO memory of your session.** It does not know:
- What the user said to you
- What files you read
- What decisions you made or why
- What you learned about the codebase
- What worked or didn't work
- What the user's preferences or quirks are

**If information is not in this document, it does not exist.**

You are not "continuing" - you are enabling a fresh agent to pick up where you left off. Write this document as if explaining the situation to a capable but completely uninformed colleague.

**RFC 2119 Keywords:**
- **MUST** - Required. Not optional. Do not skip.
- **SHOULD** - Strongly recommended unless you have a specific reason not to.
- **MAY** - Optional but often helpful.

---

```markdown
# Deep Work Session Handoff: [Topic]

**Created:** [timestamp]
**Context usage at handoff:** [X%]
**Reason for handoff:** [approaching context limit / user requested pause / etc.]

## Session Configuration (MUST complete)

You MUST restore these settings. You MUST NOT re-ask the user.

- **Commit preference:** [auto / ask / batch / none]
- **Question style:** [front-loaded / minimal / checkpoint / collaborative]
- **Scope boundaries:** [what areas to focus on, what to avoid]
- **Planned workflow:** [skill1 → skill2 → skill3]
- **Current position in workflow:** [which skill, which phase]

## User Preferences Discovered (MUST complete)

You MUST document ALL preferences learned during the session. The next agent has no way to know these otherwise.

- [e.g., "User prefers functional style over classes"]
- [e.g., "Always run tests before committing"]
- [e.g., "Keep changes minimal, avoid refactoring unrelated code"]
- [e.g., "User gets frustrated when asked obvious questions"]
- [e.g., "User wants verbose explanations of changes"]

## Task Overview (MUST complete)

**Original goal:** [what the user asked for - quote them if possible]

**Approach decided:** [high-level approach chosen during brainstorming/planning, and WHY this approach]

## Progress Summary (MUST complete)

### Completed
- [x] [Task/step 1 - brief description]
- [x] [Task/step 2 - brief description]

### In Progress
- [ ] [Current task - detailed description of state]
  - What's done: [specifics]
  - What remains: [specifics]
  - Blockers if any: [specifics]

### Remaining
- [ ] [Future task 1]
- [ ] [Future task 2]

## Files Being Modified (MUST complete)

You MUST list every file that was touched or will need to be touched.

| File | Status | Notes |
|------|--------|-------|
| `path/to/file1.ts` | Modified | [what was changed, what remains] |
| `path/to/file2.ts` | Created | [purpose, completion state] |
| `path/to/file3.ts` | Planned | [what needs to happen] |

## Key Technical Decisions (MUST complete)

You MUST document decisions AND their reasoning. The next agent may need to revisit or build on these decisions.

1. **[Decision topic]:** [What was decided] — [WHY it was decided, what alternatives were considered]
2. **[Decision topic]:** [What was decided] — [WHY it was decided, what alternatives were considered]

## Context and Constraints (MUST complete)

You MUST capture implicit knowledge that isn't obvious from the code. This is often the most valuable part of the handoff.

- [e.g., "This feature needs to work with legacy API that doesn't support X"]
- [e.g., "User mentioned they plan to deprecate Y, so don't build on it"]
- [e.g., "Performance is critical - user mentioned 10k+ items"]
- [e.g., "The tests in test_auth.py are flaky, may need retries"]
- [e.g., "Function X in file Y looks wrong but user said don't touch it"]

## Open Questions (SHOULD complete)

Questions that came up but weren't resolved:

- [ ] [Question 1 - may need to ask user]
- [ ] [Question 2 - may need to investigate]

## Test Status (MUST complete)

- **Tests passing:** [yes/no/partial - be specific]
- **New tests added:** [list with file paths]
- **Tests still needed:** [list what's missing]
- **How to run tests:** [exact command]

## Next Steps (MUST complete)

You MUST be specific. "Continue implementing" is NOT acceptable.

When resuming, the agent MUST:

1. [Exact first action - e.g., "Read src/auth/validate.ts and add refresh token handling"]
2. [Exact second action]
3. [Continue with workflow...]

## Related Files (SHOULD complete)

Reference documents that may be useful:
- Plan: `docs/plans/[plan-file].md`
- Design: `docs/plans/[design-file].md`
- Previous handoffs: `docs/deep-work-handoffs/[prior-handoff].md` (if chained)
```

---

## Writing Guidelines

### You MUST Understand This

The next agent is not you. It has:
- No memory of this conversation
- No access to your "understanding" of the situation
- No knowledge of what the user said, how they said it, or what they meant
- No awareness of failed approaches you tried
- No sense of the codebase beyond what's in its training data

**Everything in your head right now that isn't in this document WILL BE LOST.**

### MUST Follow

**Be comprehensive:** Every section marked MUST is required. Do not skip sections. Do not write "N/A" unless truly not applicable.

**Be specific:** "Modified the auth module" is USELESS. "Added JWT validation to `src/auth/validate.ts:45-67`, still need to add refresh token handling at line 89" is useful.

**Preserve reasoning:** You MUST explain WHY decisions were made, not just WHAT was decided. The next agent may need to revisit or extend these decisions.

**Include exact file paths:** The next agent MUST NOT have to search for files. Every file reference should be a complete path.

**Quote the user:** When capturing user preferences or requirements, quote their actual words when possible. Your interpretation may lose nuance.

### SHOULD Follow

**Note user quirks:** If the user has specific communication preferences, pet peeves, or working style preferences, note them. The next agent will appreciate not having to rediscover these.

**Capture implicit knowledge:** Things you learned about the codebase that aren't documented anywhere - weird dependencies, flaky tests, gotchas, undocumented behaviors.

**Explain the "obvious":** What's obvious to you right now (having just worked on this) is not obvious to a fresh agent. When in doubt, write it down.

### Common Mistakes to Avoid

- Writing "continue with the implementation" (too vague - WHAT implementation? WHERE?)
- Assuming the next agent knows which files are relevant
- Forgetting to mention things that "go without saying"
- Summarizing user preferences instead of capturing specifics
- Omitting failed approaches (the next agent may try them again)
- Skipping the "why" on technical decisions
