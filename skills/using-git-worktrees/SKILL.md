---
name: using-git-worktrees
description: Use when starting feature work that needs isolation from current workspace or before executing implementation plans - creates isolated git worktrees with smart directory selection and safety verification
---

# Using Git Worktrees

## Overview

Git worktrees create isolated workspaces sharing the same repository, allowing work on multiple branches simultaneously without switching.

**Core principle:** Systematic directory selection + safety verification = reliable isolation.


### 1. Check for Bare Repository (First Priority)

```bash
# Check if we're in a worktree of a bare repo and capture absolute path
common_dir=$(git rev-parse --git-common-dir 2>/dev/null) || {
  echo "ERROR: Failed to get git-common-dir: are you in a git repo?"
  exit 1
}
if [ -n "$common_dir" ] && grep -q "bare = true" "$common_dir/config" 2>/dev/null; then
  echo "BARE REPO DETECTED"
  echo "BARE_ROOT: $common_dir"
else
  echo "NON-BARE REPO DETECTED"
fi
```

**If failed to get git-common-dir:**
- This skill only applies to git repos. Inform user and abort this skill.

**If bare repo detected:**
- Keep track of `BARE_ROOT` for later use
- Proceed to [BARE-REPOS.md](./BARE-REPOS.md) and follow the instructions there

**If non-bare repo detected:**
- Proceed to [NON-BARE-REPOS.md](./NON-BARE-REPOS.md) and follow the instructions there
