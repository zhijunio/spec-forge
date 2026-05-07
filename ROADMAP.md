# ROADMAP

This roadmap moves `sdd-skills` from an early workflow-pack alpha into a reusable, verifiable SDD project.

## Guiding goals

- make the default SDD chain easier to adopt than assembling it manually
- keep the system skill-first and lightweight
- prove the workflow with examples before adding more abstraction
- improve consistency before expanding breadth

## Phase 0 — Rename and narrative cleanup

### Goals
- finish the move from `spec-forge` naming to `sdd-skills`
- unify the public explanation of what this repo is and is not
- remove mixed branding across plugin, hooks, and docs

### Deliverables
- rename visible branding to `sdd-skills`
- refresh README and README.zh.md
- align plugin manifest metadata and hook messages
- audit skill descriptions for old naming leftovers

## Phase 1 — v0.2: Demoable workflow pack

### Goals
- prove the end-to-end chain on one small example
- make first-time usage obvious
- add lightweight consistency checks

### Deliverables
- add `examples/` with one greenfield demo project
- include sample `docs/features/...` and `docs/plans/...` outputs
- document one full dry-run transcript or expected flow
- add a simple validation script for:
  - listed skills exist
  - plugin paths exist
  - core docs are consistent
- add minimal CI for docs and manifest sanity

### Non-goals
- broad platform support
- advanced automation
- adding many new skills

## Phase 2 — v0.3: Workflow hardening

### Goals
- tighten phase boundaries
- make review and release outputs more predictable
- reduce ambiguity around project context

### Deliverables
- standardize `sdd-review` output shape
- standardize `sdd-release` handoff summary
- add maintainer docs for editing or adding skills
- decide whether `PROJECT.md` remains the default, becomes optional, or is replaced by a documented fallback chain
- add at least one second example with a different repo shape

## Phase 3 — v0.4: Cross-agent portability

### Goals
- reduce dependence on one host environment
- keep skills portable across agent platforms

### Deliverables
- document non-Cursor installation paths
- provide platform notes for Claude Code / OpenCode / Gemini-style setups
- separate platform adapter guidance from workflow guidance
- verify the session-start story still works without Cursor-specific assumptions

## Phase 4 — v0.5: Controlled expansion

### Goals
- expand only where repeated usage proves a gap
- avoid turning the repo into an unbounded skill catalog

### Candidate work
- optional templates for feature and plan docs
- stronger review personas if needed
- context-audit mode if `PROJECT.md` is retired
- release checklists per repo type

### Rules for expansion
- no new skill unless it removes repeated workflow pain
- no new artifact unless it clearly reduces ambiguity
- no platform adapter unless at least one real user path needs it

## Open product decisions

### 1. Should `PROJECT.md` remain first-class?
Options:
- keep it as the default context baseline
- make it optional and support a README-first context chain
- replace `sdd-init` with a context-audit skill

### 2. How narrow should the repo stay?
Possible futures:
- stay as a focused SDD workflow pack
- grow into a broader engineering skill pack
- split core workflow skills from optional extras

### 3. How much should this repo standardize outputs?
Questions:
- fixed feature template or flexible prose?
- fixed review summary format?
- fixed release handoff format?

## Success criteria

The next major checkpoint is reached when:

- a new user can understand the repo from README alone
- the default chain can be demonstrated on a sample repo end-to-end
- plugin and hook metadata are internally consistent
- at least one project can use the workflow without repeated manual explanation
- the team can clearly explain why this repo exists instead of simply saying “it combines other projects”
