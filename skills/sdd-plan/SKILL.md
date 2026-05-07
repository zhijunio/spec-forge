---
name: sdd-plan
description: >
  Freeze execution into docs/plans/<yyyy-mm-dd-feature-slug>.md from a frozen feature document;
  file boundaries first, dependency order, vertical slices, checkpoints, optional parallel lanes;
  per-task Covers (verbatim) + Requirement Coverage; prose only; hand off to sdd-build.
---

# sdd-plan

Freeze execution into small, verifiable tasks with explicit acceptance criteria and explicit `read_first` files. Keep each task **self-contained** enough for an implementer with **minimal** project context, and prefer steps that fit a **single focused session** when useful.

## When to Use

- **`docs/features/<feature-slug>.md`** exists and you need implementable execution units.
- A delivery feels too large or vague to start, may be parallelized, or execution order is unclear.

**When NOT to use:** Trivial single-file changes with obvious scope, or the feature document already lists tasks you can run without a new plan file.

## Scope Check

One plan → **one coherent, verifiable deliverable**. Multiple independent subsystems → fix upstream (**`sdd-feature`**; **`sdd-brainstorm`** only if still unclear).

**Hard-stop** if requirement titles are unstable (**`sdd-feature`** first) or **`PROJECT.md`** cannot anchor build/test/lint (**`sdd-init`** or manual facts first).

## File Structure First

Before tasks, map boundaries: **create / modify / test** paths, **why** each file exists, and **shared contracts** to stabilize before parallel work. Follow project file-size conventions.

Write this map early in **`docs/plans/<yyyy-mm-dd-feature-slug>.md`** (under **Approach** or its own short subsection). Every task’s **Files likely touched** must stay **consistent** with that map.

## The Planning Process

Persist everything to **`docs/plans/<yyyy-mm-dd-feature-slug>.md`** (this repo; use `plans/` only if the repo already standardizes on it).

**Suggested file layout:** plan **header** (below) → **Approach** + file-boundary notes → **Task** sections (insert **`## Checkpoint`** groups after every **2–3** tasks or after risky work) → **Requirement Coverage** → **Parallel Execution Plan** (if any) → **Risks / Open Questions** as needed.

### Task status (plan ready ≠ tasks done)

- Completing **`sdd-plan`** means the **plan file is ready for execution** — **not** that any Task is implemented.
- Leave **every** Task **Acceptance criteria** / **Verification** checkbox as **`- [ ]`** when saving the plan. **Do not** mark tasks complete during planning.
- **Task done** = that Task’s **Verification** commands are run during **`sdd-build`** (or a worker under it) and outcomes match the plan. Only then move **`- [ ]` → `- [x]`** in **`docs/plans/...`** — **`sdd-build`** owns that update when the plan file is the **single source of truth**. If the team uses an **external tracker** as SSOT instead, mirror the same rule: **Done** only after the same verification evidence exists (see **`sdd-build`**).
- Optional: in **`## Checkpoint`** sections, add a one-line **progress note** (e.g. “through Task N”) when someone maintains the plan file alongside execution — still distinct from pre-checking future tasks.

### Step 1: Enter Plan Mode

**Read-only** until the plan file is written:

- Read **`docs/features/<feature-slug>.md`**; collect **every** requirement title (**`Covers:`** + **Requirement Coverage**).
- Read **`PROJECT.md`** → **`AGENTS.md`** → **`CLAUDE.md`** → **`README.md`** (skip missing); **`PROJECT.md`** owns stack/commands.
- Skim relevant code for patterns; map dependencies; note **Open Questions**—never invent project facts.

**Do not write implementation** during this skill—only the plan document.

### Step 2: Identify the Dependency Graph

Sketch **what depends on what**. Order tasks **bottom-up** (foundations first).

### Step 3: Slice Vertically

Prefer **thin end-to-end slices** (working behavior each step), not horizontal layers (“all DB, then all API”).

### Step 4: Write Tasks

Each task targets **minimal context** and **concrete** substeps.

**Plan header** (no implementation source—adapt names/paths):

```markdown
# [Feature Name] Implementation Plan

> **Execution default:** **`sdd-build`** orchestrates task-by-task; for **isolated** tasks use a **fresh subagent** with **`sdd-subagent-build`**. **Parallel lanes** only if Step 6 defines them and **Files likely touched** do not overlap unsafely.
> **Checkbox rule:** substeps use `- [ ]` with **exact commands** and **expected outcomes** where applicable. Leave them **unchecked** until **`sdd-build`** completes verification (**Task status** above).
> **Hard fields:** every task must include `read_first`, `acceptance_criteria`, `verification`, `dependencies`, `files likely touched`, and `estimated scope`.

**Goal:** [One sentence]
**Architecture:** [2–3 sentences]
**Tech Stack:** [Core stack]

---
```

**Task skeleton** — **`Covers:`** must quote feature-document titles **verbatim**:

```markdown
## Task [N]: [Short title]

**Covers:** [Verbatim requirement title(s)]

**read_first:** [Exact files the executor must read before touching anything]

**TDD:** yes | no

**Description:** One short paragraph.

**Acceptance criteria:**
- [ ] …

**Verification:**  ← task-level checklist (commands + expected outcomes in prose)
- [ ] …

**Dependencies:** [Task numbers or "None"]

**Files likely touched:**
- `path` — role

**Estimated scope:** XS | S | M | L | XL

<!-- If **TDD: yes**, use the five substeps below. If **TDD: no** (docs/config/chore), replace with fewer substeps that still name commands + expected outcomes. -->

- [ ] **Step 1: Write/adjust failing test**
  - Command: …
  - Expected: …
- [ ] **Step 2: Confirm failure**
  - Command: …
  - Expected: …
- [ ] **Step 3: Minimum implementation**
  - Files: …
  - Expected: …
- [ ] **Step 4: Confirm pass**
  - Command: …
  - Expected: …
- [ ] **Step 5: Atomic commit** (if git checkpoints apply)
  - Command: …
  - Expected: …
```

Example substeps (style reference):

````markdown
- [ ] **Step 2: Confirm failure**
  - Command: `pytest tests/auth/test_login.py::test_invalid_token -q`
  - Expected: `FAILED` with assertion on invalid token

- [ ] **Step 4: Confirm pass**
  - Command: `pytest tests/auth/test_login.py::test_invalid_token -q`
  - Expected: `1 passed`
````

After all tasks, add **Requirement Coverage**: **requirement title → Task N**.

Optional: **commit the plan alone** before **`sdd-build`** when the team uses phase checkpoints.

### Step 5: Order and Checkpoint

Reorder for: satisfied **Dependencies**, **working** state after each task, **fail-fast** on risky/contract work, and **`## Checkpoint`** sections every **2–3** tasks (use **project-specific** commands—not vague “tests pass” unless you name how):

```markdown
## Checkpoint: After Tasks 1–3
- [ ] Command: … — Expected: …
```

### Step 6: Parallel Execution Plan (optional)

Add when lanes help:

```markdown
## Parallel Execution Plan

### Lane A
- Task …

### Lane B
- Task …

### Sync Points
- …
```

**Rules:** no dependency edge between parallel tasks; **sync** before merge-sensitive steps; **contract task** before fan-out on shared APIs; **do not** parallelize **`sdd-build`** on overlapping files without a safe sync strategy.

Parallel lanes are only valid when each lane can move independently, owns a non-overlapping file set, has no shared mutable state, and can be verified without waiting on another lane.

**Parallelization hints:** safe—independent slices, tests on stable code, docs. Sequential—migrations, shared mutable state, long chains. Coordinate—shared contracts first, then fan out.

## Task Sizing Guidelines

| Size | Files | Example |
| --- | --- | --- |
| **XS** | 1 | Single rule / config tweak |
| **S** | 1–2 | One endpoint or component |
| **M** | 3–5 | One vertical slice |
| **L** | 5–8 | Multi-part feature—justify or split |
| **XL** | 8+ | **Split—do not ship as one task** |

Target **S** and **M** for agents.

## No Placeholders & Minimum Bar

Planning failures: **`TBD` / `TODO` / deferral** without repeating constraints; vague “add validation” or “write tests” **without** command + expectation; **“same as Task N”** without content; undefined symbols; missing **`Covers:`** or **Requirement Coverage** row for any feature title.

Minimum quality: **exact paths** in **Files likely touched**; **exact commands + expected outcomes** for behavior changes; **DRY / YAGNI** in task design.

## Common Rationalizations

| Rationalization | Reality |
| --- | --- |
| "I'll figure it out as I go" | Rework multiplies; short planning pays back fast. |
| "The tasks are obvious" | Writing them still surfaces hidden deps and edges. |
| "Planning is overhead" | Here, planning **is** the deliverable before **`sdd-build`**. |
| "I can hold it all in my head" | Written plans survive session limits and compaction. |

## Red Flags

- Implementing before **`docs/plans/...`** exists; planning from chat instead of **`docs/features/...`**.
- Mushy tasks (“implement the feature”) without **Acceptance criteria** / **Verification** / substeps with expectations.
- **XL** tasks, missing **Checkpoints**, wrong dependency order, unsafe parallel lanes.
- Rewriting requirements here—belongs in **`sdd-feature`**.

## Self-Review (author, before handoff)

1. Every feature title → some **`Covers:`** + **Requirement Coverage**.
2. Placeholder / vagueness scan across tasks.
3. Names + contracts consistent; files match **File Structure First**; lanes respect deps + **Sync Points**.

## Plan readiness (pre-`sdd-build`)

> **Note:** This checklist is for the **skill**—not the per-task **`Verification`** field inside each task.

- [ ] Each task: **Acceptance criteria**, **Verification**, **`Covers:`**, **Dependencies**, **Files likely touched**, **Estimated scope**, **TDD** + matching substeps.
- [ ] Ordering + **Checkpoints** sane; **Parallel Execution Plan** complete if used.
- [ ] **Requirement Coverage** uses **verbatim** titles; plan path is **`docs/plans/<yyyy-mm-dd-feature-slug>.md`**.
- [ ] Optional: standalone **git commit** of the plan when the workflow expects it.

## Output Template

```markdown
**Primary artifact:** `docs/plans/<yyyy-mm-dd-feature-slug>.md` (full plan layout under **The Planning Process**).

## Plan handoff

- **Plan path:** `docs/plans/<yyyy-mm-dd-feature-slug>.md`
- **Feature:** `docs/features/<feature-slug>.md` (when applicable)
- **Next:** `sdd-build` from Task 1 (or parallel lanes per **Parallel Execution Plan** when safe)
```

## Execution Handoff

Default **sequential `sdd-build`**. For **independent** tasks or **lanes**, the orchestrator may delegate each unit to **`sdd-subagent-build`** (fresh worker, narrow files). After **sync points**, continue with **`sdd-build`** / **`sdd-review`**. If readability debt is likely after verification, mention **`sdd-simplify`** post-**`sdd-review`**.

> Plan saved to **`docs/plans/<yyyy-mm-dd-feature-slug>.md`**. **`sdd-build`** task-by-task (or per parallel lane after sync). Proceed with Task 1?

## Prompt Next Step

Say next skill/path outright (question optional): **`sdd-build`** on **`docs/plans/<yyyy-mm-dd-feature-slug>.md`**.
