# superpowers-plan-review

A Claude Code plugin for **writing and reviewing implementation plans interactively**, before any code is written. It ships two separate skills:

- **`sps-plan`** — write a new plan (via `superpowers`) or open an existing one, then **review and refine it interactively** on the repo's `.md`. Finishing the plan never starts implementation.
- **`sps-pal`** — run an adversarial **Red Team Consensus** (via the PAL MCP) over a specific plan, apply only code-verified fixes, then open the interactive review.

## What it does

### `sps-plan` — write & review

- **New plan** → `superpowers:brainstorming` (understand intent first) → `superpowers:writing-plans` (decompose into small, tests-first tasks, written to a repo `.md`) → **interactive review** for feedback.
- **Existing plan** → infer which plan from the current context and confirm → **interactive review** on its `.md`.

The review is **direct and in place**: you comment section by section, Claude edits the repo's `.md`, and each edit is saved as you go. Git is the safety net — `git checkout` the file to discard a review.

### `sps-pal` — adversarial review

`sps-pal` stress-tests a **non-trivial** plan with a **Red Team Consensus**: two opposed evaluators — *The Code Inquisitor* (`against`) and *The State Pragmatist* (`neutral`) — review the plan against the real code under strict evidence rules. It then **applies only the fixes it can verify against the code** (models hallucinate APIs), records a review changelog inside the plan, and **opens the interactive review** to keep refining. Not for typos, lint, or trivial edits.

## The core rule: a finished plan is not a build order

Finishing or approving a plan (even a terse "ok", "lgtm", "go ahead", in any language) means **the plan is final, and Claude stops.** It does **not** authorize writing code, editing source, running tests, or creating migrations. Implementation happens only when you **explicitly ask for it in a later, separate turn**. This keeps "let's finalize the plan" cleanly separate from "now build it".

## Requirements

- Claude Code with plugin support.
- The [`superpowers`](https://github.com/obra/superpowers) plugin — the `sps-plan` skill calls `superpowers:brainstorming` and `superpowers:writing-plans`.
- The `pal` MCP server — only needed for the `sps-pal` skill; it provides the `consensus`, `chat`, and `listmodels` tools.

## Install

```text
/plugin marketplace add matiasAS/superpowers-plan-review
/plugin install superpowers-plan-review@matias-plugins
```

(For GitLab or self-hosted git, use the full clone URL instead of the `owner/repo` shorthand.)

## Usage

- **Write or review a plan:** `/superpowers-plan-review:sps-plan`, or just ask — e.g. "let's review the plan", "write an implementation plan for X".
- **Red team a plan:** `/superpowers-plan-review:sps-pal`, or ask to "red team the plan".

The skills keep replying in your language.

## License

MIT
