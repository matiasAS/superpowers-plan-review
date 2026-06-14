# superpowers-plan-review

A Claude Code plugin for **writing and reviewing implementation plans in plan mode**, before any code is written. It ships two separate skills:

- **`sps-plan`** — write a new plan (via `superpowers`) or open an existing one, then **review it in plan mode** with inline **"Add Comment"** feedback. On *Accept and exit plan mode*, the plan is **written to the repo's `.md`** instead of starting implementation.
- **`sps-pal`** — run an adversarial **Red Team Consensus** (via the PAL MCP) over a specific plan, apply only code-verified fixes, then hand off to `sps-plan`'s plan-mode review.

## What it does

### `sps-plan` — write & review

- **New plan** → `superpowers:brainstorming` (understand intent first) → `superpowers:writing-plans` (decompose into small, tests-first tasks), written into the **plan file** → review in plan mode.
- **Existing plan** → infer which plan from the current context and confirm → load that repo `.md` into the plan file → review in plan mode.

**How the review works:** plan mode opens a **plan file** (`~/.claude/plans/...`) in an editor tab. You review it there and leave feedback by selecting text and clicking **"Add Comment"** (anchored to the fragment); Claude applies each comment to the plan file and re-presents. On **"Accept and exit plan mode"**, Claude copies the plan file to the repo's `.md` (the canonical, git-versioned copy) — and **stops**. It does **not** implement.

### `sps-pal` — adversarial review

`sps-pal` stress-tests a **non-trivial** plan with a **Red Team Consensus**: two opposed evaluators — *The Code Inquisitor* (`against`) and *The State Pragmatist* (`neutral`) — review the plan against the real code under strict evidence rules. It then **applies only the fixes it can verify against the code** (models hallucinate APIs), records a review changelog inside the plan, and **hands off to `sps-plan`'s plan-mode review** to keep refining. Not for typos, lint, or trivial edits.

## The core rule: accepting a plan is not a build order

**"Accept and exit plan mode"** here means **the plan is written to the repo's `.md`, and Claude stops** — even with a terse "ok" / "lgtm" / "go ahead" in any language. It does **not** authorize writing code, editing source, running tests, or creating migrations (Claude ignores the "you can now start coding" prompt). Implementation happens only when you **explicitly ask for it in a later, separate turn**. This keeps "let's finalize the plan" cleanly separate from "now build it".

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
