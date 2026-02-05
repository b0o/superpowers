---
name: brainstorming
description: "You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation."
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs through natural collaborative dialogue.

Start by understanding the current project context, then ask questions one at a time to refine the idea. Once you understand what you're building, present the design in small sections (200-300 words), checking after each section whether it looks right so far.

## The Process

**Understanding the idea:**
- Check out the current project state first (files, docs, recent commits)
- Use the question tool to ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

**Exploring approaches:**
- Propose 2-3 different approaches with trade-offs
- Present options conversationally with your recommendation and reasoning
- Lead with your recommended option and explain why

**Presenting the design:**
- Once you believe you understand what you're building, present the design
- Break it into sections of 200-300 words
- Ask after each section whether it looks right so far
- Cover: architecture, components, data flow, error handling, testing
- Be ready to go back and clarify if something doesn't make sense

## After the Design

**Documentation:**
- Write the validated design to `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document (only if user opted in to commits) using superpowers:making-commits

**Implementation (if continuing):**

Use the question tool to ask how to proceed:

> "Ready to proceed with implementation?"
> 
> Options:
> 1. **Start a Deep Work session** - Set up for autonomous implementation with upfront preferences
> 2. **Interactive implementation** - Proceed step by step with regular checkpoints
> 3. **Review design first** - Let me review the design doc before proceeding

**If Deep Work session chosen:**
- **REQUIRED SUB-SKILL:** Use superpowers:deep-work-session
- The deep-work-session skill will gather preferences (commits, question style, scope)
- Then proceed to worktree setup and planning with those preferences already set
- Implementation will be autonomous based on front-loaded decisions

**If Interactive implementation chosen:**
- Use superpowers:using-git-worktrees to create isolated workspace
- Use superpowers:writing-plans to create detailed implementation plan
- Proceed with regular checkpoints and user involvement

**If Review design first chosen:**
- Point user to the saved design document path
- Wait for them to confirm they're ready to proceed
- Then offer the implementation options again

## Key Principles

- **One question at a time** - Don't overwhelm with multiple questions
- **Multiple choice preferred** - Easier to answer than open-ended when possible
- **YAGNI ruthlessly** - Remove unnecessary features from all designs
- **Explore alternatives** - Always propose 2-3 approaches before settling
- **Incremental validation** - Present design in sections, validate each
- **Be flexible** - Go back and clarify when something doesn't make sense
