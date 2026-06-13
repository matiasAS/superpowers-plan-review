---
name: superpowers-writing-plan-in-plan-mode
description: Use when the user wants to author a new implementation plan, or review and refine an existing plan, in plan mode before any code is written or executed.
---

# Writing & Reviewing Plans in Plan Mode

## Overview

Drive plan authoring and review through **plan mode** with an interactive, comment-driven loop. The plan lives in a `.md` file. The user reviews it section by section; you fold their comments into a working copy. **Approving the plan persists the revised `.md` — it does NOT start implementation.**

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
2. **REQUIRED SUB-SKILL:** Use `superpowers:writing-plans` to decompose into small tasks with file paths and tests-before-code, and write the plan to a `.md` file.
3. Enter the **Review loop** on that newly written `.md`.

## Branch B — Existing plan

1. Confirm which `.md` (inferred from context per Step 0).
2. Enter the **Review loop**.

## The Review loop (4 steps)

1. **Enter plan mode** and load the `.md` content as an editable working copy.
2. The **user comments** — section by section, task by task.
3. **Incorporate** each comment into the working copy.
4. **On accept → write the revised version back to the ORIGINAL `.md`, then STOP.**

## The load-bearing rule: accepting is NOT executing

When the user "accepts"/"approves" the plan — including casual approval **in any language** (e.g. "ok", "approve", "lgtm", "go ahead", "ship it") — that means **one thing only: write the revised plan to its `.md` file and stop.** It does NOT authorize implementation. No code, no edits to source, no running tests, no migrations.

Code is written only when the user **explicitly asks to implement, in a later, separate turn.**

**Close the loopholes — none of these justify starting to code:**

| Rationalization | Reality |
|---|---|
| "They approved the plan, obviously they want it built now." | Approval = persist the `.md`. Building is a separate request. |
| "ExitPlanMode normally means start implementing." | Not under this skill. Here it means: the revised plan is ready to be saved. |
| "It's faster to just start Task 1." | Stop. Save the `.md` and wait. |
| "They said 'go' / 'ship it' — green light to build." | Approval = save the plan and stop, not code. |

## Keep responding in the user's language

`brainstorming` and `writing-plans` are written in English. Do NOT drift into English when talking to the user — keep replying in whatever language the user is using.

## Red Flags — STOP

- About to ExitPlanMode and then edit source files / run tests → STOP. Write the `.md` and stop.
- About to ask "which plan?" when context already makes it obvious → infer + confirm instead.
- About to draft a new plan without brainstorming first → STOP. Brainstorm first.
- About to switch to English mid-conversation → keep the user's language.

## Quick Reference

| User intent | What you do |
|---|---|
| "write a plan for X" (new) | `brainstorming` → `writing-plans` → review loop |
| "review/refine the plan" (existing) | confirm which `.md` → review loop |
| user approves, in any language ("ok", "lgtm", "approve"…) (in review) | write revised `.md`, then **STOP** (no code) |
| user asks to implement (later, separate turn) | now you may execute |
