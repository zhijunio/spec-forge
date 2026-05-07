# spec-forge

An SDD-style skill repository that provides a default workflow from *
*`sdd-feature` → `sdd-plan` → `sdd-build` → `sdd-review` → `sdd-release`** (with optional **`sdd-subagent-build`**, *
*`sdd-simplify`**, **`sdd-init`**, **`sdd-brainstorm`**).

Chinese version: [README.zh.md](./README.zh.md)

## Cursor plugin

This repo ships a **Cursor plugin manifest**:

| Path                             | Purpose                                                                        |
|----------------------------------|--------------------------------------------------------------------------------|
| **`.cursor-plugin/plugin.json`** | Declares **`skills/`**, **`agents/`**, and **`hooks/hooks-cursor.json`**       |
| **`hooks/session-start`**        | Injects **`skills/use-sdd/SKILL.md`** into new Agent sessions (needs **`jq`**) |

Install options:

- **Local plugin (this machine):** copy or symlink the repo into **`~/.cursor/plugins/local/spec-forge/`** (manifest at
  **`.cursor-plugin/plugin.json`** must sit at the plugin root). Example:
  `ln -s /path/to/spec-forge ~/.cursor/plugins/local/spec-forge` — then **Developer: Reload Window** or restart Cursor.
  If a symlink does not load, use `cp -R` instead (some builds had symlink issues).
- **Remote:** Cursor **Settings → Rules → Add Rule → Remote (GitHub)** with this repository URL, or use your fork.

After install, skills are under **`skills/*/SKILL.md`**; hooks use **`hooks/hooks-cursor.json`** from the manifest.

Operating model:

- Discover when needed: **`sdd-init`**, then **`sdd-brainstorm`** if the direction is still fuzzy
- Freeze the work: **`sdd-feature`**, then **`sdd-plan`**
- Execute the plan: **`sdd-build`**; delegate isolated tasks to **`sdd-subagent-build`** only when file ownership does
  not overlap unsafely; parallelize lanes only when the plan says so **and** tasks are independent
- Verify the outcome: **`sdd-review`** for assessment only — five-step process, prefix severities); no code changes
  during review
- Refine only if needed: **`sdd-simplify`**, then re-run **`sdd-review`** if the change is non-trivial
- Ship the result: **`sdd-release`**

## Skill Guide

### `sdd-init`

- **Purpose:** build or refresh `PROJECT.md` from repository facts.
- **Input:** repo files, project conventions, and any explicit user constraints.
- **Output:** a deterministic project fact file that downstream skills can trust.
- **Flow:** auto-detect what the repo already states clearly, then ask at most one grouped clarification round for the
  rest.

### `sdd-brainstorm`

- **Purpose:** turn a fuzzy idea into a direction worth freezing.
- **Input:** a rough goal, a question, or a partial request.
- **Output:** a narrower direction, key assumptions, risks, and the smallest blocking question if needed.
- **Flow:** check repo docs or code first, then ask the least amount of clarification needed to unblock **`sdd-feature`
  **.

### `sdd-feature`

- **Purpose:** freeze scope and observable requirements into one feature document.
- **Input:** a scoped idea, `PROJECT.md`, and any brainstorm output.
- **Output:** `docs/features/<feature-slug>.md` with stable requirement titles (quoted verbatim in *
  *`sdd-plan` `Covers:`**).
- **Flow:** define the boundary, write observable behavior, list non-goals, and stop before implementation details creep
  in.

### `sdd-plan`

- **Purpose:** turn a frozen feature document into an executable implementation plan.
- **Input:** `docs/features/<feature-slug>.md`, project facts (`PROJECT.md`, …).
- **Output:** `docs/plans/<yyyy-mm-dd-feature-slug>.md` — **File Structure First**, dependency order, vertical slices,
  **Checkpoint** sections, optional **Parallel Execution Plan**, plus **Requirement Coverage** (every spec title →
  tasks).
- **Per-task fields:** **`Covers:`** (verbatim requirement titles), **`read_first`**, **Acceptance criteria**, *
  *Verification**,
  **Dependencies**, **Files likely touched**, **Estimated scope**, **TDD** + checkbox substeps (commands + expected
  outcomes).
- **Flow:** read-only until the plan file exists; prose and commands only—no implementation source in the plan; hand off
  to
  **`sdd-build`**.

### `sdd-build`

- **Purpose:** orchestrate execution of the plan task-by-task (**obra/superpowers**-style sequential plan execution).
- **Input:** `docs/plans/<yyyy-mm-dd-feature-slug>.md`, the active task packet, matching feature doc when referenced,
  `PROJECT.md` when needed.
- **Output:** implemented changes plus verification evidence for completed tasks.
- **Flow:** validate inputs and task ownership; TDD for behavior changes; keep context narrow; delegate isolated slices
  to
  **`sdd-subagent-build`** when safe.

### `sdd-subagent-build`

- **Purpose:** run **one** isolated task in a **fresh** subagent (**obra/superpowers** per-task worker pattern).
- **Input:** a single task packet: **`Covers`**, acceptance, verification, **`read_first`**, **Files likely touched**,
  etc.
- **Output:** changed files, commands run, pass/fail evidence, blockers—then return control to **`sdd-build`**.
- **Flow:** confirm ownership, execute narrowly, verify, report—no replanning or formal review inside the worker.

### `sdd-review`

- **Purpose:** multi-axis merge readiness review — five axes; findings use **Critical / (required) / Nit / Optional /
  FYI** per **`skills/sdd-review/SKILL.md`** Step
  4 ([code-review-and-quality](https://github.com/addyosmani/agent-skills/blob/main/skills/code-review-and-quality/SKILL.md)).
- **Scope:** judge **the change under review** — typically **diff / changed files**, **tests that cover it**, and the
  author’s **verification story**, against **spec or task** intent (**`skills/sdd-review/SKILL.md`**). *
  *`docs/features/`** and **`docs/plans/`** are **not** a default full-document audit; treat them as authoritative only
  when you explicitly commission spec alignment (same signal as **`skills/use-sdd/SKILL.md`**). Use *
  *`skills/sdd-review/references/`** for extra depth when the change warrants it — not a mandatory sweep of the whole
  repo.
- **Input:** spec or task, tests, and implementation (see SKILL **Review Process**).
- **Output:** categorized feedback and verdict; optional **Review Summary** template in **`agents/code-reviewer.md`**.
  Deep lists: **`skills/sdd-review/references/`** (security, performance, accessibility, orchestration).
- **Flow:** read-only reviewer role; optionally dispatch **`agents/code-reviewer.md`** + SKILL in a fresh context when
  your tooling supports subagents.

### `sdd-simplify`

- **Purpose:** reduce complexity without intended behavior change—only inside an explicitly agreed simplify scope.
- **Scope:** **behavior-preserving** edits only (**`skills/sdd-simplify/SKILL.md`**). Work inside a **named scope** (
  paths, module, “this PR”) agreed up front; prefer **recent / task-related** code and avoid **drive-by** refactors
  outside that scope. **`docs/features/`** and **`docs/plans/`** are optional context — not the default rewrite target
  unless you ask.
- **Input:** user-named scope (paths, module, “this change”) + code in scope; **`docs/features/`** and **`docs/plans/`**
  optional context only.
- **Output:** simpler code + evidence behavior is unchanged.
- **Flow:** cut only in range; re-**`sdd-review`** if the boundary is vague or the refactor is large.

### `sdd-release`

- **Purpose:** package an approved change set for handoff.
- **Input:** reviewed (and optionally simplified) work, fresh verification evidence.
- **Output:** checklist + user-facing summary (version, exclusions, handoff choice).
- **Flow:** freeze candidate, notes, rollback hints—no “done” without evidence from this session.

## How to Test

This repository is mostly documentation and skill files. The most practical check is to walk the full skill chain on a
small greenfield example project.

Use something like this as an opening prompt for a dry run:

```text
I want a simple task-management feature.
Freeze a feature spec, turn it into a plan, implement it task-by-task,
review the changes, and tell me if it's safe to ship.
```

## Source Mapping

| Skill                | Reference source                                                                                             |
|----------------------|--------------------------------------------------------------------------------------------------------------|
| `sdd-init`           | [sivaprasadreddy/sdd-skills](https://github.com/sivaprasadreddy/sdd-skills)                                  |
| `sdd-brainstorm`     | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-feature`        | [obra/superpowers](https://github.com/obra/superpowers) · [OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| `sdd-plan`           | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-build`          | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-subagent-build` | [obra/superpowers](https://github.com/obra/superpowers)                                                      |
| `sdd-review`         | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills)                                        |
| `sdd-simplify`       | [zhijunio/sdd-skills](https://github.com/zhijunio/sdd-skills)                                                |
| `sdd-release`        | [obra/superpowers](https://github.com/obra/superpowers) · [OpenSpec](https://github.com/Fission-AI/OpenSpec) |

## Reference Reading

- [OpenSpec getting started](https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md) — source-of-truth
  specs, proposal/application/archive flow, and context hygiene.
- [GSD README](https://github.com/gsd-build/get-shit-done/blob/main/README.md) — workflow controls, parallelization, and
  subagent injection.
- [GSD plan phase](https://github.com/gsd-build/get-shit-done/blob/main/get-shit-done/workflows/plan-phase.md) — hard
  task templates with `read_first` and `acceptance_criteria`.
- [GSD verify work](https://github.com/gsd-build/get-shit-done/blob/main/get-shit-done/workflows/verify-work.md) —
  incremental verification and UAT-style feedback loops.
- [superpowers](https://github.com/obra/superpowers) — review before merge, verification before completion, and parallel
  dispatch rules.
- [sdd-skills](https://github.com/sivaprasadreddy/sdd-skills) — `sdd-init` bootstrap patterns and project-context
  capture.
