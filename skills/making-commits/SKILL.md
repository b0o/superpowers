---
name: making-commits
description: Use when ready to commit code changes. Covers conventional commit format, commit sizing, GPG failures, and push safety. Other skills invoke this for commit mechanics.
---

# Making Commits

Commit mechanics when committing is allowed. For WHEN to commit, see `COMMIT_PREFERENCE` in deep-work-session.

**Core principle:** Small, focused commits. Verify first. Never push without asking.

## Explicit Invocation

If user explicitly asks to commit (not via deep work session) with substantial changes (5+ files):

> "I see substantial changes. Before committing, what's your preference?"
> Options: Single commit / Multiple commits (by logical unit) / Let me review what to include

## Conventional Commit Format

```
type(scope): description   # lowercase, imperative, <72 chars, no period
```

| Type | Use |
|------|-----|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `refactor` | Neither fix nor feature |
| `test` | Tests |
| `chore` | Maintenance |

## Commit Sizing

- One logical change per commit
- Break up: unrelated changes, bug+feature mixed, refactor+behavior mixed
- Keep together: test+implementation for same feature

## Pre-Commit

1. **Verify** - Run tests, build if applicable
2. **Review diff** - `git diff --staged`
3. **No secrets** - No API keys, passwords, credentials
4. **No debug code** - No console.log, debugger

**REQUIRED:** superpowers:verification-before-completion for significant changes.

## GPG Signing Failure

If `gpg failed to sign the data`:

**Use question tool:**
> "GPG signing failed. How to proceed?"
> Options: Retry / Skip signing (--no-gpg-sign) / Abort

**Never** silently skip or retry.

## Push Safety

**Never push without explicit permission.**

**Use question tool before any push:**
> "Ready to push to [remote/branch]. Confirm?"
> Options: Yes, push / No, don't push

**Force push:** Warn about overwriting remote history, require explicit confirmation.

**Never:**
- Push automatically after commit
- Push to main/master without confirmation
- Force push without warning
- Push commits you didn't create

## Quick Reference

| Situation | Action |
|-----------|--------|
| GPG fails | Ask user (question tool) |
| Want to push | Ask user (question tool) |
| Large changeset | Break into smaller commits |
| Secrets in diff | Remove, never commit |

## Integration

**Delegating skills:** writing-plans, subagent-driven-development, implementer-prompt

**Required:** verification-before-completion (before significant commits)

**Preference system:** `COMMIT_PREFERENCE` = WHETHER to commit; this skill = HOW
