# superpowers-writing-plan-in-plan-mode

A Claude Code plugin that adds a skill for **authoring and reviewing implementation plans in plan mode**, through an interactive, comment-driven loop — where **approving a plan saves the revised file instead of starting implementation.**

## What it does

When you want to write a new plan or refine an existing one, the skill drives a consistent flow:

- **New plan** → `superpowers:brainstorming` (understand intent first) → `superpowers:writing-plans` (decompose into small, tests-first tasks, written to a `.md`) → the review loop.
- **Existing plan** → infer which plan from the current context and confirm → the review loop.

**The review loop (4 steps):**

1. Enter plan mode and load the `.md` as an editable working copy.
2. You comment — section by section, task by task.
3. Your feedback is folded into the working copy.
4. **On accept, the revised plan is written back to the original `.md` — and that's it.**

## The core rule: accepting is NOT executing

Approving the plan (even a terse "ok", "dale", "lgtm") means **one thing**: persist the revised `.md` and stop. It does **not** authorize writing code, editing source, running tests, or creating migrations. Implementation happens only when you **explicitly ask for it in a later, separate turn**. This keeps "let's finalize the plan" cleanly separate from "now build it".

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
