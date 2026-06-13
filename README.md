# superpowers-writing-plan-in-plan-mode

A Claude Code plugin that adds a skill for **authoring and reviewing implementation plans in plan mode**, through an interactive, comment-driven loop — where **approving a plan saves the revised file instead of starting implementation.**

## What it does

When you want to write a new plan or refine an existing one, the skill drives a consistent flow:

- **New plan** → `superpowers:brainstorming` (understand intent first) → `superpowers:writing-plans` (decompose into small, tests-first tasks, written to a `.md`) → the review loop.
- **Existing plan** → infer which plan from the current context and confirm → the review loop.

**The review loop (interactive, in plan mode):**

The canonical plan lives in the repo `.md`. The skill copies its full content to an **outside working copy** (the plan-mode file in your plans folder), you review it interactively in plan mode, and on approval it's **written back to the repo `.md`**. Exiting plan mode never starts implementation.

1. The repo `.md`'s full content is copied to the plan-mode working copy.
2. You comment — section by section, task by task — and the working copy is edited interactively.
3. **On accept (exit plan mode), the revised plan is written back to the repo's `.md`. No code until you explicitly ask, in a later turn.**

## The core rule: accepting is NOT executing

Approving the plan (even a terse "ok", "lgtm", "go ahead", in any language) means **one thing**: persist the revised `.md` and stop. It does **not** authorize writing code, editing source, running tests, or creating migrations. Implementation happens only when you **explicitly ask for it in a later, separate turn**. This keeps "let's finalize the plan" cleanly separate from "now build it".

## Requirements

- Claude Code with plugin support.
- The [`superpowers`](https://github.com/obra/superpowers) plugin — the skill calls `superpowers:brainstorming` and `superpowers:writing-plans`.

## Install

```text
/plugin marketplace add matiasAS/superpowers-writing-plan-in-plan-mode
/plugin install superpowers-writing-plan-in-plan-mode
```

(For GitLab or self-hosted git, use the full clone URL instead of the `owner/repo` shorthand.)

## Usage

Invoke it explicitly with `/superpowers-writing-plan-in-plan-mode`, or just ask to write or review a plan — e.g. "let's review the plan", "write an implementation plan for X". The skill keeps replying in your language.

## License

MIT
