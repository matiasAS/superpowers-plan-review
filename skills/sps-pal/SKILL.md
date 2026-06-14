---
name: sps-pal
description: Use when adversarially reviewing a specific, non-trivial implementation plan before coding — runs a "Red Team Consensus" via the PAL MCP (two opposed evaluators with strict evidence rules), applies only code-verified fixes, then opens the interactive review of the plan.
---

# Red Team Consensus — Adversarial Plan Review (PAL)

## Overview

Apply a **Red Team Consensus** — an adversarial review via the `pal` MCP — to **a specific plan**, BEFORE any code is written. Two evaluators with opposed stances stress-test the plan against the real code under strict evidence rules. When the review finishes, you **apply only the code-verified fixes** to the plan's `.md` and **open the interactive review** so the user keeps refining it with feedback.

(Requires the `pal` MCP server for `consensus` / `chat` / `listmodels`. "Open the interactive review" hands off to the `sps-plan` skill (existing-plan path), which opens the `.md` in **plan mode** for **"Add Comment"** review.)

## When to Use

- Adversarially reviewing a **non-trivial** refactor/feature plan that already exists as an `.md`.
- For a **large** refactor (≥5 days / multi-phase / cross-cutting), run this *before* coding (and, if your workflow commits plans, commit the reviewed plan first).
- **NOT** for typos / lint / doc-only / trivial 1-line edits.

**Precondition:** the plan should already be authored — ideally via `superpowers:writing-plans`, **grounded in the real code** (LSP + direct reading) and **not anchored to previous plan variants** (re-reading an old plan contaminates it with anchoring bias). `pal` reviews a plan; it does not author one.

## Inputs

- **The specific plan** to review (the `.md` — absolute path).
- **The real relevant code** (absolute paths) so the evaluators verify against the repo, not against assumptions.

## Running the consensus (operational)

- **Check availability first:** run `listmodels` to see which providers/models are actually configured.
- **`models`:** at least two, with differentiated stances. The template's two **Perspectives** map to the two models' `stance_prompt`:
  - `{ model: <A>, stance: "against",  stance_prompt: "<The Code Inquisitor — break the plan: structural flaws, resource bottlenecks, regressions, loss of parity>" }`
  - `{ model: <B>, stance: "neutral",  stance_prompt: "<The State Pragmatist / devil's advocate — dynamic flaws: race conditions, over-engineering, state failures, ignored logical edge cases>" }`
- **`relevant_files`:** absolute paths to **the plan + the real relevant code**.
- **`step` (step 1):** the filled-in Red Team Consensus prompt — the exact text every model sees. Steps 2+: capture each model's response. The final step synthesizes.
- **Workflow shape:** `total_steps` = number of models + 1 (final synthesis); set `next_step_required: false` on the synthesis step.
- **Concrete failure prediction:** make each evaluator give **one** "most likely to fail" prediction — it forces a specific risk instead of a soft, confirmation-biased verdict.

## What the orchestrator (Claude) fills in

- the **plan description**,
- the **Locked Decisions** (already decided by the user / settled in prior rounds — these are **NOT** re-litigated),
- the **classification**: `[REFACTOR]` / `[NEW FEATURE]` / `[BUG FIX]`.

## Post-review — do NOT skip

Synthesize the findings, then:

1. **FILTER false positives by verifying against the code.** Do **not** apply blindly — models hallucinate APIs, callers, and signatures.
2. **Run the gates the review suggests** (e.g. a `grep`/LSP enumeration of callers) **for real**, and record the result. Never leave a gate as theory.
3. **Apply ONLY the verified fixes** to the plan's `.md`.
4. **Leave a review changelog inside the plan:** what was found, what was applied, what was discarded and why.
5. **Hand off to `sps-plan`** (existing-plan path): it opens the `.md` in **plan mode** so the user keeps refining it with **"Add Comment"**. Do **not** start implementation — the plan is still a plan.

## Operational notes

- **Models / routing:** if a direct endpoint returns 429 / quota errors, route to an alternative model; re-check with `listmodels`. Don't hard-fail on one provider.
- **Empty evaluator:** if `consensus` returns `verdict: null` for a model (provider hiccup), retry **that** model via `chat` with the same `relevant_files`.
- **Verify, don't trust:** a recommended gate is **executed** (`grep`/LSP) and its result recorded — not left as a hypothesis.
- **Language:** respond to the user in their language; the **proposed code stays in English.**

## The prompt — "Red Team Consensus" (template)

> Fill in `[INSERT PLAN DESCRIPTION HERE]`, the **Locked Decisions**, and the **classification**. The full plan goes as `relevant_files`, not inline.

```
Act as the orchestrator of a "Red Team Consensus", using your MCP tools to stress-test and refine the following proposed plan:

[INSERT PLAN DESCRIPTION HERE]

**Step 1: Context Ingestion**
Use your tools (file read, AST search, grep) to map the involved files, the dependency tree, signatures, workers, and affected interfaces.

**Locked Decisions (DO NOT TOUCH):**
1. [e.g. Keep strict compatibility with the current Postgres and RabbitMQ]
2. [e.g. Do not alter the unified Dockerfile build process]
3. [e.g. Keep the standard HTTP response convention]

---

### 🔍 Step 2: Classification and Dynamic Focus
First, classify the plan into ONE of these categories and force the evaluators to focus their analysis:
*   **[REFACTOR]:** Focus on loss of functional parity, broken contracts, and incomplete migrations.
*   **[NEW FEATURE]:** Focus on integration, domain coupling, persistence, and observability.
*   **[BUG FIX]:** Focus on regressions, insufficient coverage, and partially-fixed edge cases.

---

### 🧠 Step 3: Plan Assumptions
Before the debate, list the assumptions the plan takes as true. Classify them strictly as:
*   **[Verified]:** There is evidence in the code (e.g. "the worker reads from this queue").
*   **[Unverified]:** Assumed but not guaranteed (e.g. "the payload always has the ID field").

---

### 🛡️ The Debate: Evaluation Perspectives

**Perspective 1: The Code Inquisitor (Stance: AGAINST)**
**Goal:** Break the plan by hunting for structural flaws, resource bottlenecks, regressions, and loss of parity.

**Perspective 2: The State Pragmatist (Stance: NEUTRAL / DEVIL'S ADVOCATE)**
**Goal:** Hunt for dynamic flaws: race conditions, over-engineering, state failures, and ignored logical edge cases.

---

### 🚫 Absolute Evidence Rules (for both models)
Every finding MUST be backed by verifiable evidence in the current repository (a file, symbol, interface, import, caller, worker, or test). If you cannot prove it by reading the code via MCP, you are forbidden from classifying it as Critical or Important.

---

### 📋 Expected Output (Strict Format)

Produce a unified report without nesting lists unnecessarily.

**### Assumptions Detected**
*   [Verified] - [Assumption]
*   [Unverified] - [Assumption] -> Risk: [Brief description]

**### Code Findings**

**[🔴 CRITICAL]** *(Regressions, broken contracts, imminent failures)*
* `[File:Line or Symbol]` | **Issue:** [Description]
  -> **Impact:** [Why it breaks]
  -> **Affected Components:** [Explicitly list affected consumers, workers, or calls. DO NOT ASSUME without identifying them.]
  -> **Concrete Fix:** [Precise instruction to patch]

**[🟠 IMPORTANT]** *(Severe over-engineering, probable race conditions, tech debt)*
* `[File:Line or Symbol]` | **Issue:** [Description]
  -> **Affected Components:** [List affected]
  -> **Impact:** [Why it degrades the system]

**[🟡 MINOR]** *(Conventions, readability)*
* `[File or Module]` | **Issue:** [Description]

**[🔵 NEEDS VERIFICATION]** *(Theoretical risks)*
* `[Module/Concept]` | Possible risk but no evidence found in the reviewed scope via MCP. Requires manual review.

---

### ⚖️ Verdict

*   **[ APPROVE ]**
*   **[ APPROVE WITH FIXES ]**
*   **[ BLOCK ]**

**Reason:** [1 to 3 lines justifying the verdict]

**Required Fixes Before Execution (only if applicable):**
*   [Mandatory fix 1]
*   [Mandatory fix 2]

**Most Likely To Fail (one concrete prediction):**
*   [The single thing we recommend that is most likely to fail in practice, and why]

*(Only if the verdict is APPROVE or APPROVE WITH FIXES, then generate the terminal commands or editor actions to start execution.)*
```

## Red Flags — STOP

- About to apply findings **without verifying against the code** → STOP. Models hallucinate APIs/callers.
- About to leave a recommended gate **as theory** → STOP. Run it (`grep`/LSP) and record the result.
- About to run `pal` on a **typo / trivial** edit → it doesn't apply.
- Finished the consensus but **not opening the interactive review** → STOP. After the review you hand off to the interactive review.
- About to skip the **review changelog** → STOP. Record found / applied / discarded.
- About to start **implementing** after the review → STOP. The plan is still just a plan.

## Quick Reference

| Step | What you do |
|---|---|
| Inputs | the specific plan `.md` + the real relevant code (absolute paths) |
| Review | `consensus`: 2 models, `against` + `neutral` stances, `relevant_files` = plan + real code, Red Team Consensus prompt in `step` |
| Empty verdict | retry that model via `chat` with the same `relevant_files` |
| Post-review | filter false positives vs. code → run gates for real → apply only verified → changelog in the plan |
| After review | open the interactive review of the `.md`; **no code** until explicitly asked |
