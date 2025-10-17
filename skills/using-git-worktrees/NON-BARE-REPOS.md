# Using Git Worktrees In Non-Bare Repos


## Critical: Pre-Requisites

You should have come here from [Using Git Worktrees skill](./SKILL.md) and determined that you're in a non-bare git repo.
If not, please follow the instructions there first, and only continue with these instructions if you
determine that you're in a non-bare git repo.
## Overview

Git worktrees create isolated workspaces sharing the same repository, allowing work on multiple branches simultaneously without switching.

**Core principle:** Systematic directory selection + safety verification = reliable isolation.

**Announce at start:** "Non-bare Git repo detected. I'm using the Using Git Worktrees skill to set up an isolated workspace."

## ⚠️ Critical: Working Directory Behavior

**Claude Code's PWD does NOT change between tool calls.**

**The scenario:** Claude Code starts in the main project directory. You create a worktree at `.worktrees/feat-auth`. Running `cd` does NOT move you to the worktree for future tool calls.

**The solution:** Chain commands with `&&` to ensure they run in the worktree directory.

```bash
# ✅ Correct - chain commands together
cd "$WORKTREE_PATH" && npm install && npm test

# ❌ Wrong - cd doesn't persist
cd "$WORKTREE_PATH"
npm install  # Runs in original PWD, not worktree!
```

**For file operations (Read/Edit/Write tools):**
- Use absolute paths: `$WORKTREE_PATH/src/file.js`
- Or use relative paths if you understand where Claude Code's PWD is

**Remember:** You are always in Claude Code's original PWD unless you chain commands with `&&`.

## Directory Selection Process

Follow this priority order:

### 1. Check Existing Directories

```bash
# Check in priority order
ls -d .worktrees 2>/dev/null     # Preferred (hidden)
ls -d worktrees 2>/dev/null      # Alternative
```

**If found:** Use that directory. If both exist, `.worktrees` wins.

### 2. Check CLAUDE.md

```bash
grep -i "worktree.*director" CLAUDE.md 2>/dev/null
```

**If preference specified:** Use it without asking.

### 3. Ask User

If no directory exists and no CLAUDE.md preference:

```
No worktree directory found. Where should I create worktrees?

1. .worktrees/ (project-local, hidden)
2. ~/.config/superpowers/worktrees/<project-name>/ (global location)

Which would you prefer?
```

## Safety Verification

### For Project-Local Directories (.worktrees or worktrees)

**MUST verify .gitignore before creating worktree:**

```bash
# Check if directory pattern in .gitignore
grep -q "^\.worktrees/$" .gitignore || grep -q "^worktrees/$" .gitignore
```

**If NOT in .gitignore:**

Per Jesse's rule "Fix broken things immediately":
1. Add appropriate line to .gitignore
2. Commit the change
3. Proceed with worktree creation

**Why critical:** Prevents accidentally committing worktree contents to repository.

### For Global Directory (~/.config/superpowers/worktrees)

No .gitignore verification needed - outside project entirely.

## Creation Steps

### 1. Detect Project Name

```bash
project=$(basename "$(git rev-parse --show-toplevel)")
```

### 2. Create Worktree and Capture Path

```bash
# Determine full path and store in WORKTREE_PATH variable
case $LOCATION in
  .worktrees|worktrees)
    WORKTREE_PATH="$LOCATION/$BRANCH_NAME"
    ;;
  ~/.config/superpowers/worktrees/*)
    WORKTREE_PATH="~/.config/superpowers/worktrees/$project/$BRANCH_NAME"
    ;;
esac

# Create worktree with new branch
git worktree add "$WORKTREE_PATH" -b "$BRANCH_NAME"
```

### 3. Run Project Setup (Chained Commands)

Auto-detect and run appropriate setup using `&&` to chain commands:

```bash
# Node.js
cd "$WORKTREE_PATH" && [ -f package.json ] && npm install

# Rust
cd "$WORKTREE_PATH" && [ -f Cargo.toml ] && cargo build

# Python
cd "$WORKTREE_PATH" && [ -f requirements.txt ] && pip install -r requirements.txt
cd "$WORKTREE_PATH" && [ -f pyproject.toml ] && poetry install

# Go
cd "$WORKTREE_PATH" && [ -f go.mod ] && go mod download

# ... etc: use the appropriate tool for your project
```

### 4. Verify Clean Baseline (Chained Commands)

Run tests to ensure worktree starts clean:

```bash
# Examples - use project-appropriate command
cd "$WORKTREE_PATH" && npm test
cd "$WORKTREE_PATH" && cargo test
cd "$WORKTREE_PATH" && pytest
cd "$WORKTREE_PATH" && go test ./...
# ... etc: use the appropriate tool for your project
```

**If tests fail:** Report failures, ask whether to proceed or investigate.

**If tests pass:** Report ready.

### 5. Working With Files in the Worktree

**Use absolute paths with Read/Edit/Write tools:**

```bash
# ✅ Correct - using $WORKTREE_PATH
Read: $WORKTREE_PATH/src/auth.js
Edit: $WORKTREE_PATH/src/config.js
Write: $WORKTREE_PATH/tests/new_test.js

# ❌ Wrong - assumes you're "in" the worktree (you're not)
Read: src/auth.js
Edit: src/config.js
Write: tests/new_test.js
```

**For bash commands:**

```bash
# ✅ Correct - chain with cd
cd "$WORKTREE_PATH" && npm run lint
cd "$WORKTREE_PATH" && cargo check

# ❌ Wrong - cd doesn't persist
cd "$WORKTREE_PATH"  # This does nothing for the next tool call
npm run lint  # Runs in original PWD, not worktree
```

### 6. Report Location

```
Worktree ready at $WORKTREE_PATH
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>

Use $WORKTREE_PATH for all file operations.
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| `.worktrees/` exists | Use it (verify .gitignore) |
| `worktrees/` exists | Use it (verify .gitignore) |
| Both exist | Use `.worktrees/` |
| Neither exists | Check CLAUDE.md → Ask user |
| Directory not in .gitignore | Add it immediately + commit |
| Running commands in worktree | Chain with `&&`: `cd "$WORKTREE_PATH" && command` |
| Reading/editing files | Use absolute path: `$WORKTREE_PATH/src/file.js` |
| Tests fail during baseline | Report failures + ask |
| No package.json/Cargo.toml | Skip dependency install |

## Common Mistakes

**Using relative paths for file operations**
- **Problem:** `Read: src/file.js` assumes you're "in" the worktree (you're not)
- **Fix:** Always use absolute paths: `Read: $WORKTREE_PATH/src/file.js`

**Expecting `cd` to persist across tool calls**
- **Problem:** Running `cd "$WORKTREE_PATH"` in one tool call doesn't affect the next
- **Fix:** Chain commands: `cd "$WORKTREE_PATH" && npm test`

**Not capturing worktree path in a variable**
- **Problem:** Hard to use absolute paths consistently without storing the path
- **Fix:** Store path in `$WORKTREE_PATH` variable immediately after creation

**Skipping .gitignore verification**
- **Problem:** Worktree contents get tracked, pollute git status
- **Fix:** Always grep .gitignore before creating project-local worktree

**Assuming directory location**
- **Problem:** Creates inconsistency, violates project conventions
- **Fix:** Follow priority: existing > CLAUDE.md > ask

**Proceeding with failing tests**
- **Problem:** Can't distinguish new bugs from pre-existing issues
- **Fix:** Report failures, get explicit permission to proceed

**Hardcoding setup commands**
- **Problem:** Breaks on projects using different tools
- **Fix:** Auto-detect from project files (package.json, etc.)

## Example Workflow

```
You: I'm using the Using Git Worktrees skill to set up an isolated workspace.

[Check .worktrees/ - exists]
[Verify .gitignore - contains .worktrees/]
[Set WORKTREE_PATH: /Users/jesse/myproject/.worktrees/auth]
[Create worktree: git worktree add "$WORKTREE_PATH" -b feature/auth]
[Run: cd "$WORKTREE_PATH" && npm install]
[Run: cd "$WORKTREE_PATH" && npm test - 47 passing]

Worktree ready at /Users/jesse/myproject/.worktrees/auth
Tests passing (47 tests, 0 failures)
Ready to implement auth feature

Use $WORKTREE_PATH for all file operations.
```

## Red Flags

**Never:**
- Use relative paths for file operations (assumes you're "in" the worktree)
- Expect `cd` to persist across tool calls
- Skip capturing worktree path in `$WORKTREE_PATH` variable
- Create worktree without .gitignore verification (project-local)
- Skip baseline test verification
- Proceed with failing tests without asking
- Assume directory location when ambiguous
- Skip CLAUDE.md check

**Always:**
- Use `$WORKTREE_PATH` for all file operations (Read/Edit/Write)
- Chain commands with `&&` when running in worktree: `cd "$WORKTREE_PATH" && command`
- Capture worktree path in `$WORKTREE_PATH` variable immediately after creation
- Follow directory priority: existing > CLAUDE.md > ask
- Verify .gitignore for project-local
- Auto-detect and run project setup
- Verify clean test baseline

## Integration

**Called by:**
- skills/collaboration/brainstorming (Phase 4)
- Any skill needing isolated workspace

**Pairs with:**
- skills/collaboration/finishing-a-development-branch (cleanup)
- skills/collaboration/executing-plans (work happens here)
