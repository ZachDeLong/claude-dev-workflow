---
name: checkpoint
description: "Use when pausing work mid-session or before ending a session. Saves a snapshot of progress so the next session can pick up where you left off."
metadata:
  author: zachd
  version: "1.1.0"
---

# Session Checkpoint

## Overview

Save a snapshot of the current session's progress. What's done, what's in progress, what's next, and what to watch out for. Saves to a file so the next session can read it and pick up seamlessly.

<HARD-GATE>
Do NOT continue working on the task after creating the checkpoint. The point is to PAUSE and save state.
Do NOT make up progress. Only document what actually happened in this session.
Do NOT skip saving the file. The whole point is persistence across sessions.
</HARD-GATE>

## Process

### Step 1: Analyze the session

Silently review the conversation history. Identify:

- What was the user working on?
- What's been completed?
- What's currently in progress (started but not finished)?
- What's planned but not started?
- What gotchas, blockers, or important context was discovered?
- What files were modified?
- Are there uncommitted changes?
- **Depth check:** Was this a complex session? (multiple decisions, dead ends, architecture discussions, approaches tried and abandoned, important context discovered). If yes, include the full Context & Decisions section.

### Step 2: Check for uncommitted changes

Run `git status` and `git diff --stat` to see if there are unsaved changes. Include this in the checkpoint — the next session needs to know if there's work that hasn't been committed.

### Step 3: Output the checkpoint

Print the checkpoint in chat AND save it to a file.

Use this format:

```markdown
# Session Checkpoint

**Date:** [current date]
**Project:** [project name from CLAUDE.md or package.json or directory name]
**Working on:** [one-line summary of the overall task]

## Completed
- [Specific thing that was finished — reference files if relevant]
- [Another completed item]

## In Progress
- [Thing that was started but not finished — describe current state]
- [What specifically is left to do on this item]

## Next Up
- [Planned work that hasn't been started]
- [In priority order]

## Watch Out For
- [Gotchas discovered during the session]
- [Important context the next session needs]

## Context & Decisions (include if session was complex)
- [Key decision made and WHY — so the next session doesn't re-debate it]
- [Approach that was tried and didn't work — so it's not tried again]
- [Architecture or design understanding gained during the session]
- [Things that were discussed but intentionally deferred — and why]

## Uncommitted Changes
- [List of modified/new files from git status, or "All changes committed"]

## Files Touched
- [Key files that were created or modified this session]
```

### Step 3b: Decide on depth

**Simple sessions** (quick bug fix, single feature, no dead ends): skip the "Context & Decisions" section. Keep it tight.

**Complex sessions** (multiple decisions, approaches tried, architecture discussions, dead ends encountered, important discoveries): include "Context & Decisions." This is the difference between a bookmark and cliff notes — the next session needs this context to not repeat mistakes or re-debate settled decisions.

When in doubt, include it. Too much context is better than too little for a session handoff.

### Step 4: Save the checkpoint file

Save to `.claude/checkpoint.md` in the project root (create the `.claude/` directory if it doesn't exist).

If a previous checkpoint exists, overwrite it — checkpoints are snapshots, not a log.

After saving, confirm: "Checkpoint saved to `.claude/checkpoint.md`. Your next session will see this when it starts."

### Step 5: Suggest next session kickoff

End with a one-liner the user can paste into their next session to resume:

> **To resume:** "Read .claude/checkpoint.md and pick up where I left off. Start with [the most important next item]."

## For the NEXT session (auto-detection)

If you start a session and notice `.claude/checkpoint.md` exists in the project, read it and tell the user:

"I found a checkpoint from a previous session. Here's where you left off:"

Then summarize the checkpoint briefly and ask: "Want to pick up from here?"

This makes the handoff seamless — the user doesn't have to remember to tell you about the checkpoint.

## Key Principles

- **Accurate over optimistic** — if something is half-done, say it's half-done, not "almost finished"
- **Specific over vague** — "the API route returns data but the frontend isn't connected yet" not "working on the feature"
- **Include context** — decisions made, gotchas found, approaches tried. The next session has zero memory.
- **Include uncommitted changes** — critical for the next session to know the repo state
- **One checkpoint at a time** — overwrite, don't append. It's a snapshot, not a journal.
