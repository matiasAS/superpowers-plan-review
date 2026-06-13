---
name: sps-plan
description: Use when the user wants to author a new implementation plan, or review and refine an existing one — interactively, directly on the repo's `.md`, before any code is written or executed.
---

# Writing & Reviewing Implementation Plans

## Overview

Author and review an implementation plan through a **direct, interactive review on the repo's `.md` file**: the user comments section by section, you edit the file in place, and each edit is saved as you go. The load-bearing rule: **finishing the review does NOT start implementation** — the plan stays a plan until the user explicitly asks to build it, in a later turn.

(Requires the `superpowers` plugin for the `brainstorming` and `writing-plans` sub-skills.)

## When to Use

- The user asks to write/draft a new implementation plan for a feature or refactor.
- The user asks to review, refine, or comment on an existing plan.
- Trigger phrases (in any language): "let's review the plan", "refine the plan", "write a plan for X".

## The flow

Always the same three steps. Only **Step 1** differs, depending on whether the plan already exists.

### Step 0 — Decide: new plan or existing plan?

Decide from the **current conversation context** — don't ask cold:

- If you've been working on a specific feature/topic, the plan is that one. **Infer it, then confirm** — don't ask "which plan?" when context makes it obvious, and don't silently assume either.
- **Stay within the current context.** If the session is about feature A, don't plan unrelated feature B.
- Only if genuinely ambiguous, ask with one short AskUserQuestion.

### Step 1 — Get the plan into a repo `.md`

- **New plan** (create it):
  1. **REQUIRED SUB-SKILL** `superpowers:brainstorming` FIRST — explore intent, requirements, and a design the user approves. Write nothing until then.
  2. **REQUIRED SUB-SKILL** `superpowers:writing-plans` — decompose into small tasks with file paths and tests-before-code, and write the plan to a `.md` file **in the repo**.
- **Existing plan** (don't create it): it already lives in the repo, so **skip brainstorming + writing-plans** — there is no creation step. Just identify which repo `.md` it is (inferred + confirmed per Step 0).

### Step 2 — Review the `.md` interactively

Review the plan **directly on the repo's `.md`** (normal mode — no plan mode, no separate working copy). The **user comments** — section by section, task by task — and you **edit the `.md` in place**; each edit is saved immediately. Git is the safety net: if the user wants to discard the review, that's a `git checkout` of the file.

### Step 3 — Finish the review

When the user is satisfied / approves (even a terse "ok", "lgtm", "go ahead", in any language): the plan is **already saved** (you edited it in place), so finishing just **ends the review**. It does **NOT** start implementation.

## The load-bearing rule: finishing the review does NOT execute

When the user approves / says the plan looks good — including casual approval **in any language** (e.g. "ok", "approve", "lgtm", "go ahead", "ship it") — that means **the plan is final, and you stop.** No code, no edits to source, no running tests, no migrations.

Code is written only when the user **explicitly asks to implement, in a later, separate turn.**

| Rationalization | Reality |
|---|---|
| "The plan looks done, obviously they want it built now." | A finished plan is still just a plan. Building is a separate request. |
| "They approved the plan — green light to code." | Approval = the plan is final. It is NOT authorization to implement. |
| "It's faster to just start Task 1." | Stop. The review is over; wait for an explicit build request. |
| "They said 'go' / 'ship it'." | That ends the review, it does not start the build. |

## Keep responding in the user's language

`brainstorming` and `writing-plans` are written in English. Do NOT drift into English when talking to the user — keep replying in whatever language the user is using.

## Red Flags — STOP

- About to start coding / edit source / run tests / create migrations after the plan is approved → STOP. A finished plan is not a build order.
- About to summarize the plan or make a "map" instead of editing the real `.md` → STOP. Edit the actual file in place.
- About to copy the plan elsewhere or set up a separate "working copy" → don't. Edit the repo `.md` directly.
- About to ask "which plan?" when context already makes it obvious → infer + confirm instead.
- About to draft a new plan without brainstorming first → STOP. Brainstorm first.
- About to switch to English mid-conversation → keep the user's language.

## Quick Reference

| User intent | What you do |
|---|---|
| "write a plan for X" (new) | Step 1: `brainstorming` → `writing-plans` → repo `.md`; then review (Steps 2–3) |
| "review/refine the plan" (existing) | Step 1: confirm which repo `.md`; then review (Steps 2–3) |
| user approves / says it's done | the plan is already saved; **STOP** (no code) |
| user asks to implement (later, separate turn) | now you may execute |
