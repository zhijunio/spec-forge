---
name: use-sdd
description: >
  Entry map for sdd-skills: default SDD chain and when to use each skill. Read first in a new session to route work.
---

# sdd-skills workflow map

**Default chain:** **`sdd-feature` → `sdd-plan` → `sdd-build` → `sdd-review` → `sdd-release`**

Optional: **`sdd-init`**, **`sdd-brainstorm`**, **`sdd-subagent-build`**, **`sdd-simplify`**.

## Operating model

| Phase | Skill | Role |
|-------|--------|------|
| Context | **`sdd-init`** | Refresh **`PROJECT.md`** when facts are missing or stale |
| Explore | **`sdd-brainstorm`** | Narrow fuzzy ideas before **`sdd-feature`** |
| Freeze | **`sdd-feature`** then **`sdd-plan`** | Spec + executable plan |
| Implement | **`sdd-build`** | Run the plan; **`sdd-subagent-build`** only for isolated tasks when safe; **`- [ ]` → `- [x]`** on plan **Verification** only **after** commands pass in session (**not** at **`sdd-plan`** time) |
| Verify | **`sdd-review`** | Read-only transcript; intent from packet/diff — **not** default audit of **`docs/features/`** / **`docs/plans/`** |
| Refine | **`sdd-simplify`** | Reduce complexity without behavior change; re-**`sdd-review`** if non-trivial |
| Ship | **`sdd-release`** | Handoff / release notes |

Full detail: repo **`README.md`** (and **`README.zh.md`**).

## Agents (optional subagent dispatch)

- **`agents/code-reviewer.md`** — use with **`skills/sdd-review/SKILL.md`** for Code Reviewer persona + deliverable shape.
