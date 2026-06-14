---
name: sps-plan
description: Use when the user wants to author a new implementation plan, or review and refine an existing one, in Claude Code's plan mode — the plan is reviewed on the plan file with "Add Comment", and on accept it is written to the repo's .md instead of starting implementation.
---

# Reviewing Implementation Plans in Plan Mode

## Overview

Author and review an implementation plan through Claude Code's interactive **plan mode**. Two files are in play:

- the **plan file** (`~/.claude/plans/<name>.md`) — the working copy plan mode opens in an editor tab; the **only file writable while in plan mode**, and where the user leaves inline **"Add Comment"** feedback;
- the **repo `.md`** — the canonical, git-versioned plan.

The one twist: **"Accept and exit plan mode" is redefined here to "write the plan to the repo `.md`"** — it never starts implementation.

(Requires the `superpowers` plugin for the `brainstorming` and `writing-plans` sub-skills.)

## When to Use

- The user asks to write/draft a new implementation plan for a feature or refactor.
- The user asks to review, refine, or comment on an existing plan.
- Trigger phrases (in any language): "write a plan for X", "let's review the plan", "refine the plan".

## The flow

### Step 0 — New plan or existing plan?

Decide from the **current conversation context** — infer, then confirm; don't ask cold, and don't silently assume. Stay within the current context (don't plan an unrelated topic).

### Step 1 — Enter plan mode and load the plan into the plan file

Enter plan mode (`EnterPlanMode`). Plan mode creates a **plan file** at `~/.claude/plans/<name>.md` and opens it in a tab — it is the **only file you can write** while in plan mode. Get the plan content into that plan file:

- **New plan** (create it):
  1. **REQUIRED SUB-SKILL** `superpowers:brainstorming` FIRST (*before* entering plan mode) — explore intent, requirements, and a design the user approves.
  2. **REQUIRED SUB-SKILL** `superpowers:writing-plans`, **with its output pointed at the plan file** (`~/.claude/plans/<name>.md`). Pass that path as the plan location — it overrides writing-plans' default (`docs/superpowers/plans/`). ⚠️ Letting it write the repo default while in plan mode **fails** (only the plan file is writable there).
- **Existing plan** (don't create it):
  - Identify which **repo `.md`** it is (inferred + confirmed per Step 0; if genuinely ambiguous, one short AskUserQuestion). **Skip** brainstorming + writing-plans — there is no creation step.
  - **Copy the repo `.md`'s CURRENT content** into the plan file — the user's live version, with any edits they made; the repo `.md` is the **source of truth**. **Never** reuse a stale plan file from a previous session.

### Step 2 — Review interactively with "Add Comment"

Call `ExitPlanMode` to present the plan. The user reviews it in the plan-file tab and leaves feedback by **selecting text and using "Add Comment"** (anchored to fragments). Those comments come back to you — **apply each one to the plan file**, then present again with `ExitPlanMode`. Loop (present → apply comments → present) as many times as needed until the user is satisfied.

### Step 3 — On "Accept and exit plan mode" → write to the repo `.md`

When the user accepts and exits plan mode: **copy the plan file → the repo `.md`, then STOP.**

- New plan → write to the canonical repo location (e.g. `docs/superpowers/plans/<date>-<feature>.md`, or wherever the user keeps plans).
- Existing plan → write back to the **same** repo `.md` it was loaded from.

Confirm the file was written. Then **STOP — do NOT implement.**

## The load-bearing rule: accept = write the repo `.md`, NOT execute

When the user accepts / exits plan mode — including casual approval **in any language** (e.g. "ok", "approve", "lgtm", "go ahead", "ship it") — that means **one thing only: copy the plan to the repo `.md` and stop.** No code, no edits to source, no running tests, no migrations.

Code is written only when the user **explicitly asks to implement, in a later, separate turn.**

| Rationalization | Reality |
|---|---|
| "Accept and exit plan mode means start implementing." | Not here. This skill redefines accept → write the plan to the repo `.md`. |
| "Claude Code printed 'you can now start coding'." | Ignore it. Here, accept = persist the plan to the repo, not build it. |
| "They approved the plan, obviously they want it built now." | Approval = the plan is final in the repo. Building is a separate, later request. |
| "It's faster to just start Task 1." | Stop. Write the `.md` and wait. |

## Keep responding in the user's language

`brainstorming` and `writing-plans` are written in English, and the plan content stays in English. But do NOT drift into English when talking to the user — keep replying in whatever language they use.

## Red Flags — STOP

- About to implement / edit source / run tests after accept → STOP. Accept = write the repo `.md`, not execute (even if Claude Code says "you can now start coding").
- About to point `writing-plans` at its default (`docs/...`) while in plan mode → STOP, it will fail; point it at the plan file.
- About to reuse a stale plan file for an existing plan → STOP, load the current repo `.md` (source of truth).
- About to summarize the plan instead of loading its FULL content → STOP, load it whole.
- About to ask "which plan?" when context makes it obvious → infer + confirm instead.
- About to draft a new plan without brainstorming first → STOP, brainstorm first.
- About to switch to English mid-conversation → keep the user's language.

## Quick Reference

| User intent | What you do |
|---|---|
| "write a plan for X" (new) | `brainstorming` → `EnterPlanMode` → `writing-plans` into the plan file → review → accept = write the repo `.md` |
| "review/refine the plan" (existing) | `EnterPlanMode` → copy the repo `.md` into the plan file → review → accept = write back to that repo `.md` |
| user leaves "Add Comment" feedback | apply each comment to the plan file, then `ExitPlanMode` again |
| Accept and exit plan mode | copy plan file → repo `.md`, then **STOP** (no code) |
| user asks to implement (later, separate turn) | now you may execute |
