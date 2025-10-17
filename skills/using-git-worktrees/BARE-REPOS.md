# Using Git Worktrees In Bare Repos

## Critical: Pre-Requisites

You should have come here from [Using Git Worktrees skill](./SKILL.md) and determined that you're in a bare repo.
If not, please follow the instructions there first, and only continue with these instructions if you
determine that you're in a bare git repo.

## Overview

**Announce at start:** "Bare Git repo detected. I'm using the Using Git Worktrees skill to set up an isolated workspace."

## ⚠️ Critical: Working Directory Behavior

**Claude Code's PWD does NOT change between tool calls.**

**The scenario:** Claude Code starts in worktree A (`/bare-root/worktree/main`). You create worktree B (`/bare-root/worktree/feat-auth`). Running `cd` does NOT move you to worktree B for future tool calls.

**The solution:** Capture the **absolute path** to the new worktree immediately, then use it everywhere.

```bash
# Step 1: Capture absolute paths first
bare_root=$(git rev-parse --git-common-dir)
WORKTREE_PATH="$bare_root/worktree/$BRANCH_NAME"

# Step 2: Create worktree using absolute path
git -C "$bare_root" worktree add "$WORKTREE_PATH" -b "$BRANCH_NAME"

# Step 3: ALL subsequent commands use $WORKTREE_PATH
cd "$WORKTREE_PATH" && npm install && npm test
```

**For file operations (Read/Edit/Write tools):**
- Use `$WORKTREE_PATH/src/file.js` (absolute)
- Never use `src/file.js` (assumes you're "in" the worktree - you're not)

**Remember:** You are always in Claude Code's original PWD unless you chain commands with `&&`.

## Directory Selection Process

Follow this priority order:

### 1. Check for Bare Repository (First Priority)

```bash
# Check if we're in a worktree of a bare repo and capture absolute path
common_dir=$(git rev-parse --git-common-dir 2>/dev/null)
if [ -n "$common_dir" ] && grep -q "bare = true" "$common_dir/config" 2>/dev/null; then
  # Capture absolute path to bare root
  BARE_ROOT=$(cd "$common_dir" && pwd)
  use_bare_workflow=true
fi
```

**If bare repo detected:**
- `$BARE_ROOT` now contains absolute path (e.g., `/home/user/proj/myproject`)
- Use `worktree/` subdirectory at bare repository root
- No .gitignore verification needed (git manages this structure)
- Proceed to Creation Steps

**If NOT bare repo:**
- This workflow only applies to bare repositories
- Inform user and do not create worktrees
- Standard git workflow applies

**Note:** Branch names with slashes (e.g., `feat/auth`) create nested directories (`worktree/feat/auth`). Most examples use single-level names like `feat-auth` or `fix-memory-leak`.

## Safety Verification

### For Bare Repository Worktrees

No .gitignore verification needed - git manages `worktree/` directory structure automatically.

## Creation Steps

### 1. Capture Absolute Worktree Path

```bash
# Build absolute path to new worktree
WORKTREE_PATH="$BARE_ROOT/worktree/$BRANCH_NAME"
```

### 2. Create Worktree Using Absolute Path

```bash
# Create worktree with new branch using absolute path
# Git handles nested directories automatically (e.g., feat/auth → worktree/feat/auth)
git -C "$BARE_ROOT" worktree add "$WORKTREE_PATH" -b "$BRANCH_NAME"
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
Read: $WORKTREE_PATH/src/auth.rs
Edit: $WORKTREE_PATH/src/config.js
Write: $WORKTREE_PATH/tests/new_test.rs

# ❌ Wrong - assumes you're "in" the worktree (you're not)
Read: src/auth.rs
Edit: src/config.js
Write: tests/new_test.rs
```

**For bash commands:**

```bash
# ✅ Correct - chain with cd
cd "$WORKTREE_PATH" && cargo check
cd "$WORKTREE_PATH" && npm run lint

# ❌ Wrong - cd doesn't persist
cd "$WORKTREE_PATH"  # This does nothing for the next tool call
cargo check  # Runs in original PWD, not worktree
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
| In worktree of bare repo | Capture `$BARE_ROOT` absolute path, create `$WORKTREE_PATH` |
| NOT in bare repo | Inform user, do not create worktrees |
| Running commands in worktree | Chain with `&&`: `cd "$WORKTREE_PATH" && command` |
| Reading/editing files | Use absolute path: `$WORKTREE_PATH/src/file.js` |
| Branch name with slashes | Git handles automatically (e.g., `feat/auth` → `worktree/feat/auth`) |
| Tests fail during baseline | Report failures + ask |
| No package.json/Cargo.toml | Skip dependency install |

## Common Mistakes

**Using relative paths for file operations**
- **Problem:** `Read: src/file.js` assumes you're "in" the worktree (you're not)
- **Fix:** Always use absolute paths: `Read: $WORKTREE_PATH/src/file.js`

**Expecting `cd` to persist across tool calls**
- **Problem:** Running `cd "$WORKTREE_PATH"` in one tool call doesn't affect the next
- **Fix:** Chain commands: `cd "$WORKTREE_PATH" && npm test`

**Not capturing absolute paths early**
- **Problem:** Using relative `worktree/branch` when Claude Code starts in a different worktree
- **Fix:** Capture `$BARE_ROOT` and `$WORKTREE_PATH` as absolute paths immediately

**Not detecting bare repo**
- **Problem:** Tries to create worktree in wrong location
- **Fix:** Always check `git-common-dir` for bare repo first

**Using worktrees in non-bare repos**
- **Problem:** This workflow only applies to bare repositories
- **Fix:** Detect non-bare repos and inform user

**Proceeding with failing tests**
- **Problem:** Can't distinguish new bugs from pre-existing issues
- **Fix:** Report failures, get explicit permission to proceed

**Hardcoding setup commands**
- **Problem:** Breaks on projects using different tools
- **Fix:** Auto-detect from project files (package.json, Cargo.toml, etc.)

**Using bare-incompatible git commands**
- **Problem:** Commands like `git rev-parse --show-toplevel` fail in bare repos
- **Fix:** Run commands from within worktree, or use bare-safe alternatives

## Example Workflow

```
You: I'm using the Using Git Worktrees skill to set up an isolated workspace.

[Check git-common-dir - bare repo detected]
[Capture BARE_ROOT: /home/user/proj/myproject]
[Set WORKTREE_PATH: /home/user/proj/myproject/worktree/feat-auth]
[Create worktree: git -C "$BARE_ROOT" worktree add "$WORKTREE_PATH" -b feat-auth]
[Run: cd "$WORKTREE_PATH" && cargo build]
[Run: cd "$WORKTREE_PATH" && cargo test - 47 passing]

Worktree ready at /home/user/proj/myproject/worktree/feat-auth
Tests passing (47 tests, 0 failures)
Ready to implement auth feature

Use $WORKTREE_PATH for all file operations.
```

## Red Flags

**Never:**
- Use relative paths for file operations (assumes you're "in" the worktree)
- Expect `cd` to persist across tool calls
- Skip capturing absolute paths for `$BARE_ROOT` and `$WORKTREE_PATH`
- Skip bare repo detection
- Create worktrees in non-bare repos
- Skip baseline test verification
- Proceed with failing tests without asking
- Use git commands incompatible with bare repos

**Always:**
- Capture `$BARE_ROOT` and `$WORKTREE_PATH` as absolute paths immediately
- Use `$WORKTREE_PATH` for all file operations (Read/Edit/Write)
- Chain commands with `&&` when running in worktree: `cd "$WORKTREE_PATH" && command`
- Detect bare repo first (check git-common-dir)
- Create worktree using absolute path: `git -C "$BARE_ROOT" worktree add "$WORKTREE_PATH" -b branch`
- Auto-detect and run project setup in new worktree
- Verify clean test baseline

## Integration

**Called by:**
- skills/collaboration/brainstorming (Phase 4)
- Any skill needing isolated workspace

**Pairs with:**
- skills/collaboration/finishing-a-development-branch (cleanup)
- skills/collaboration/executing-plans (work happens here)
