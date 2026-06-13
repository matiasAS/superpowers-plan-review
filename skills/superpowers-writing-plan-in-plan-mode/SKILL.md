---
name: superpowers-writing-plan-in-plan-mode
description: Use when the user wants to author a new implementation plan, or review and refine an existing plan, before any code is written or executed.
---

# Writing & Reviewing Plans in Plan Mode

## Overview

Author and review implementation plans through the IDE's interactive **plan-mode** flow — enter plan mode, iterate with the user, exit to approve. The only twist: plan mode's normal **"exit → execute"** is redefined here to **"exit → write the revised plan to the repo's `.md`"**. Approving the plan persists the document; it never starts implementation.

This skill orchestrates two superpowers sub-skills (`brainstorming`, `writing-plans`) plus a review loop. (Requires the `superpowers` plugin for the sub-skills.)

## When to Use

- The user asks to write/draft a new implementation plan for a feature or refactor.
- The user asks to review, refine, or comment on an existing plan.
- Trigger phrases (in any language): "let's review the plan", "refine the plan", "write a plan for X".

## Step 0 — New plan or existing plan?

Decide from the **current conversation context**:

- If you've been working on a specific feature/topic, the plan is almost certainly that one. **Infer it, then confirm** — don't ask cold ("which plan?") when context makes it obvious, and don't silently assume the file either.
- **Stay within the current context.** Do NOT plan an unrelated topic (if the session is about feature A, don't draft a plan for unrelated feature B).
- If genuinely ambiguous, ask with one short AskUserQuestion.

## Branch A — New plan

1. **REQUIRED SUB-SKILL:** Use `superpowers:brainstorming` FIRST. Do not write any plan until you've explored intent, requirements, and a design the user approves.
2. **REQUIRED SUB-SKILL:** Use `superpowers:writing-plans` to decompose into small tasks with file paths and tests-before-code, and write the plan to a `.md` file **in the repo** (the canonical location).
3. Enter the **Review loop** on that repo `.md`.

## Branch B — Existing plan

The plan already exists in the repo, so **skip brainstorming + writing-plans** — there is no creation step.

1. Confirm which repo `.md` (inferred from context per Step 0).
2. Enter the **Review loop** directly.

## The Review loop (interactive, in plan mode)

The canonical plan lives in the repo `.md`. You review it on an **outside working copy** (in the user's plans folder — the plan-mode file), then write the approved result back to the repo. That separation is what lets plan mode's interactive sandbox edit the working copy while the repo file stays untouched until approval.

1. **Enter plan mode and copy the repo `.md`'s FULL content** into the plan-mode working file (the outside copy, in the user's folder). Load it **verbatim** — no summary, no "map", and don't ask how to set up the copy. This outside copy is what you edit.
2. The **user comments** — section by section, task by task — and you edit the outside working copy interactively.
3. **On accept (the user approves / you exit plan mode) → write the working copy back to the repo's ORIGINAL `.md`, then STOP.** Exiting persists the revised plan to the repo; it does **NOT** start implementation.

## The load-bearing rule: exiting persists the plan, it does NOT execute

When the user approves the plan and you exit plan mode — including casual approval **in any language** (e.g. "ok", "approve", "lgtm", "go ahead", "ship it") — that means **one thing only: write the revised plan to the repo's `.md` and stop.** Exiting plan mode here does **NOT** mean "start implementing". No code, no edits to source, no running tests, no migrations.

Code is written only when the user **explicitly asks to implement, in a later, separate turn.**

**Close the loopholes — none of these justify starting to code:**

| Rationalization | Reality |
|---|---|
| "Exiting plan mode means start implementing." | Not here. This skill redefines exit → write the plan to the repo `.md`. |
| "They approved the plan, obviously they want it built now." | Approval = persist the plan to the repo. Building is a separate request. |
| "It's faster to just start Task 1." | Stop. Persist the plan to the `.md` and wait. |
| "They said 'go' / 'ship it' — green light to build." | Approval = persist the plan, not code. |

## Keep responding in the user's language

`brainstorming` and `writing-plans` are written in English. Do NOT drift into English when talking to the user — keep replying in whatever language the user is using.

## Red Flags — STOP

- About to start coding / edit source / run tests / create migrations after exiting plan mode → STOP. Exit = write the plan to the repo `.md`, not execute.
- About to summarize the plan or make a "map" instead of loading its FULL content → STOP. Load the whole plan as the working copy.
- About to ask the user how to set up the working copy → don't. Load the full plan into the plan-mode working copy.
- About to ask "which plan?" when context already makes it obvious → infer + confirm instead.
- About to draft a new plan without brainstorming first → STOP. Brainstorm first.
- About to switch to English mid-conversation → keep the user's language.

## Quick Reference

| User intent | What you do |
|---|---|
| "write a plan for X" (new) | `brainstorming` → `writing-plans` → review loop |
| "review/refine the plan" (existing) | confirm which `.md` → review loop (plan mode, full content) |
| user approves / you exit plan mode | write the revised plan to the repo `.md`, then **STOP** (no code) |
| user asks to implement (later, separate turn) | now you may execute |
