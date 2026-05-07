# Project: spec-forge

## Mission

This repository ships SDD-style skills (Markdown under `skills/`) plus companion READMEs so agents can run **`sdd-feature` → `sdd-plan` → `sdd-build` → `sdd-review` → `sdd-release`** and related skills. It is not an application runtime; the “product” is the skill files and documentation.

## Project Facts

### Classification

- Repo type: Workflow/Skill Pack

### Evidence (sources of truth)

- Manifests consulted: none (no `package.json`, `Makefile`, or language build files in this repo)
- Docs consulted: `README.md`, `README.zh.md`, `skills/**/SKILL.md`
- Maintainer-confirmed: none
- Uncertain / needs confirmation: none

### Technical baseline

- Tech stack: Markdown documentation; no compiled code in-tree
- Configuration: none
- Build & test tooling: sanity-only shell snippets in **Commands** (`test`, `find`, `grep`) — no package manager
- Deployment / runtime entrypoints: none
- Data / external state: none

## Architecture

Single documentation and skill pack: each skill is a `SKILL.md` with front matter and process text. `docs/features/` and `docs/plans/` are the intended locations for feature/plan artifacts when the workflow is used; the root `PROJECT.md` is the project fact file for SDD.

## Source Layout

```text
.
├── PROJECT.md
├── README.md
├── README.zh.md
├── AGENTS.md
├── CLAUDE.md
└── skills/
    ├── sdd-init/
    ├── sdd-feature/
    ├── sdd-plan/
    ├── sdd-build/
    ├── sdd-subagent-build/
    ├── sdd-review/
    ├── sdd-simplify/
    ├── sdd-release/
    ├── sdd-brainstorm/
    └── … (each contains SKILL.md)
```

## Commands

Documentation-only repo: no compiler or package scripts. The following are **sanity checks** (exit `0` when the repo layout is intact); they were verified locally.

### Build

```bash
test -f README.md && test -d skills
```

### Test

```bash
test -f skills/sdd-init/SKILL.md
```

### Start

```bash
true
```

(No runnable app — placeholder success.)

### Lint

```bash
find skills -name SKILL.md -print -quit | grep -q .
```

(At least one `SKILL.md` exists under `skills/`.)

## Conventions

- Naming: skill folders use kebab-case (`sdd-init`, …); each skill has `SKILL.md`
- Documentation: root `PROJECT.md`; feature and plan docs live under `docs/features/` and `docs/plans/` when produced by the workflow
- Error handling: n/a (documentation repo)
- Authentication/Security: n/a
- SDD workflow notes: match the appropriate `sdd-*` skill; repo workflow controls stay in `README.md`

## Dependency Policy

- Runtime approved: none (no runtime dependencies in-repo)
- Dev/build approved: none required for this repo as documented
- Requires explicit approval: any new dependency or toolchain added later (e.g. CI, linters) should be recorded here after explicit decision

## External Services / Interfaces

none

## Open Questions

none
