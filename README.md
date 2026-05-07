# sdd-skills

`sdd-skills` is a **skill-first SDD workflow pack** for AI coding agents. It provides a default path from **feature** → **plan** → **build** → **review** → **release**, with optional support for **init**, **brainstorm**, **subagent execution**, and **simplify**.

Chinese version: [README.zh.md](./README.zh.md)

## What this project is

This repository is not an app, SDK, or code generator. It is a **workflow repository**:

- a curated set of `sdd-*` skills
- a default operating model for multi-step software delivery
- a lightweight Cursor plugin entrypoint
- a repo-level convention layer for agents that need consistent handoff between planning, implementation, review, and release

Default chain:

- `sdd-feature`
- `sdd-plan`
- `sdd-build`
- `sdd-review`
- `sdd-release`

Optional support skills:

- `sdd-init`
- `sdd-brainstorm`
- `sdd-subagent-build`
- `sdd-simplify`

## Why this project exists

Strong upstream projects already exist:

- **OpenSpec / spec-kit** provide spec-driven phases and formal artifacts
- **superpowers** provides execution discipline, TDD, subagent workflows, and review loops
- **agent-skills** provides broad engineering skills across implementation, review, simplify, and shipping
- the upstream **sivaprasadreddy/sdd-skills** project inspired the bootstrap idea for a project-context baseline

This repo exists because there is still a practical gap between those building blocks and day-to-day agent usage:

- some systems are **spec-heavy** and more command-centric than we want
- some are **broad skill libraries** rather than a default SDD chain
- some are strong at execution but do not define one lightweight end-to-end workflow pack
- many repos still rely on agents to reconstruct project facts from scattered files every session

`sdd-skills` narrows that gap by packaging a **default, opinionated, reusable SDD workflow** that is light enough to embed into repo-level agent setups.

## Why not just use mainstream SDD projects directly?

You can. This repository is not arguing that upstream projects are wrong. The point is narrower:

> many teams want a **default path** more than they want a large menu of equally valid paths.

### Why not just use OpenSpec or spec-kit?

Those systems are strong when you want heavier spec artifacts, explicit proposal flows, and more formal change-management structure.

This repo intentionally chooses a lighter shape:

- fewer required artifacts
- skill-first routing instead of a command-first mental model
- a simpler feature → plan → build → review → release chain
- easier embedding into agent rule files and plugin hooks

### Why not just use superpowers?

`superpowers` is one of the most useful upstream inspirations here, especially for:

- plan execution
- TDD discipline
- subagent task isolation
- review loops

But `superpowers` is primarily a **powerful skill library**, not a single default SDD product surface. `sdd-skills` turns that execution discipline into a more explicit workflow chain with repo-local naming and handoff rules.

### Why not just use agent-skills?

`agent-skills` is broader than this repo. That is a strength, but also a different goal.

This project deliberately optimizes for:

- one default SDD chain
- narrower phase boundaries
- easier repo-level adoption
- less skill sprawl when the main goal is structured software delivery

## Design principles

### 1. Skill-first, not command-first

Commands may exist as entrypoints, but skills are the real abstraction boundary. The workflow should still make sense even if the host platform changes.

### 2. Default chain over skill sprawl

This repo is not trying to catalog every possible engineering skill. It focuses on the minimum set needed to carry work from frozen scope to reviewed handoff.

### 3. Clear phase boundaries

Each phase should answer one question:

- `sdd-feature`: what are we building?
- `sdd-plan`: how will we build it?
- `sdd-build`: can we execute the plan safely?
- `sdd-review`: is the change ready?
- `sdd-release`: how do we hand it off cleanly?

### 4. TDD and verification are mandatory for behavior changes

Implementation should not collapse into unverified code generation. The workflow should keep failing-proof → minimum fix → passing-proof as the default rhythm.

### 5. Fresh context for isolated work

Subagents are useful when tasks are truly isolated. Shared-file chaos is not a feature.

### 6. Lightweight structure beats artifact bureaucracy

The system should be structured enough to reduce drift, but not so heavy that agents spend more time feeding templates than delivering software.

### 7. Repo facts should be explicit

If the repo needs a project-context baseline, it should be written down instead of repeatedly guessed.

## Operating model

- Discover when needed: `sdd-init`, then `sdd-brainstorm` if direction is still fuzzy
- Freeze the work: `sdd-feature`, then `sdd-plan`
- Execute the plan: `sdd-build`
- Delegate isolated slices only when safe: `sdd-subagent-build`
- Verify merge readiness without editing code: `sdd-review`
- Reduce complexity only if needed: `sdd-simplify`
- Package the result for handoff: `sdd-release`

## Cursor plugin

This repo ships a **Cursor plugin manifest**:

| Path | Purpose |
|---|---|
| `.cursor-plugin/plugin.json` | Declares `skills/`, `agents/`, and `hooks/hooks-cursor.json` |
| `hooks/session-start` | Injects `skills/use-sdd/SKILL.md` into new Agent sessions (needs `jq`) |

Install options:

- **Local plugin (this machine):** copy or symlink the repo into `~/.cursor/plugins/local/sdd-skills/` (manifest at `.cursor-plugin/plugin.json` must sit at the plugin root). Example: `ln -s /path/to/sdd-skills ~/.cursor/plugins/local/sdd-skills` — then **Developer: Reload Window** or restart Cursor.
- **Remote GitHub rule import:** Cursor **Settings → Rules → Add Rule → Remote (GitHub)** with this repository URL or your fork. Treat this as a lighter rule-loading path, not necessarily as a full local-plugin equivalent.

After **local plugin install**, skills are under `skills/*/SKILL.md`; hooks use `hooks/hooks-cursor.json` from the manifest. For **remote rule import**, verify separately whether your Cursor build loads hooks and plugin metadata the same way.

## Skill guide

### `sdd-init`
- Purpose: build or refresh a project-context baseline from repository facts.
- Output: a deterministic fact file that downstream skills can trust.

### `sdd-brainstorm`
- Purpose: turn a fuzzy idea into a direction worth freezing.
- Output: clarified scope, assumptions, risks, and smallest blocking questions.

### `sdd-feature`
- Purpose: freeze scope and observable requirements into one feature document.
- Output: `docs/features/<feature-slug>.md`.

### `sdd-plan`
- Purpose: turn a frozen feature into an executable implementation plan.
- Output: `docs/plans/<yyyy-mm-dd-feature-slug>.md`.

### `sdd-build`
- Purpose: orchestrate task-by-task execution of the plan.
- Output: implemented changes plus verification evidence.

### `sdd-subagent-build`
- Purpose: execute one isolated plan task in a fresh subagent.
- Output: task-local changes, evidence, and blockers.

### `sdd-review`
- Purpose: merge-readiness review across correctness, simplicity, architecture, security, and performance.
- Output: graded findings plus verdict.

### `sdd-simplify`
- Purpose: reduce complexity without intended behavior change.
- Output: simpler code plus evidence that behavior stayed stable.

### `sdd-release`
- Purpose: package an approved change set for handoff.
- Output: release or handoff checklist plus summary.

## If you remove `PROJECT.md`, what should replace it?

**Current default:** this repo still treats `PROJECT.md` as the primary project-context baseline.

You can remove `PROJECT.md`, but then you should replace it with a **stable context chain**, not with guesswork.

Recommended fallback order:

1. `README.md` — project mission, top-level usage, repo layout
2. manifests and config files — stack, commands, dependencies
3. `AGENTS.md` / `CLAUDE.md` — agent behavior rules only
4. feature and plan docs — task-local intent, not project-wide facts

If you choose this route:

- treat `README.md` as the project-facts anchor
- keep build/test/lint commands accurate there
- avoid putting feature requirements into README
- keep `AGENTS.md` and `CLAUDE.md` focused on behavior rules, not duplicated repo facts
- make `sdd-init` optional or later redefine it as a **context audit** skill instead of a file-generation skill

Short version:

> removing `PROJECT.md` is fine only if repo facts still have one stable, low-ambiguity home.

## How to test

This repository is mostly documentation and skill files. The most practical check is to walk the full skill chain on a small greenfield example project.

Use something like this as an opening prompt for a dry run:

```text
I want a simple task-management feature.
Freeze a feature spec, turn it into a plan, implement it task-by-task,
review the changes, and tell me if it's safe to ship.
```

## Source mapping

| Skill | Reference source |
|---|---|
| `sdd-init` | [sivaprasadreddy/sdd-skills](https://github.com/sivaprasadreddy/sdd-skills) |
| `sdd-brainstorm` | [obra/superpowers](https://github.com/obra/superpowers) |
| `sdd-feature` | [obra/superpowers](https://github.com/obra/superpowers) · [OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| `sdd-plan` | [obra/superpowers](https://github.com/obra/superpowers) |
| `sdd-build` | [obra/superpowers](https://github.com/obra/superpowers) |
| `sdd-subagent-build` | [obra/superpowers](https://github.com/obra/superpowers) |
| `sdd-review` | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) |
| `sdd-simplify` | [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) |
| `sdd-release` | [obra/superpowers](https://github.com/obra/superpowers) · [OpenSpec](https://github.com/Fission-AI/OpenSpec) |

## Reference reading

- [OpenSpec getting started](https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md)
- [GSD README](https://github.com/gsd-build/get-shit-done/blob/main/README.md)
- [GSD plan phase](https://github.com/gsd-build/get-shit-done/blob/main/get-shit-done/workflows/plan-phase.md)
- [GSD verify work](https://github.com/gsd-build/get-shit-done/blob/main/get-shit-done/workflows/verify-work.md)
- [superpowers](https://github.com/obra/superpowers)
- [sdd-skills](https://github.com/sivaprasadreddy/sdd-skills)
