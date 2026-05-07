---
name: sdd-subagent-build
description: >
  Worker skill: one isolated task from docs/plans/ with a frozen file set,
  TDD-first when behavior changes, run declared verifications, return evidence.
---

# sdd-subagent-build

This skill is for a **worker** subagent, not the orchestrator. It receives one task, one narrow file set, and one verification target.

## When to Use

- A single task from `sdd-plan` is independent enough to hand off
- The owned files do not overlap unsafely with another active task
- You want one task executed with a minimal context window

## Core Rules

- Own only the file set declared for the task
- Do not change the plan, feature spec, or review scope
- Do not widen the task into adjacent cleanup work
- Return concrete evidence, not just a status statement

## Process

### 1. Confirm Ownership

- Read the task's `Covers`, `Acceptance criteria`, `Verification`, and `Files likely touched`
- Restate the owned files and the intended outcome
- If ownership is unclear, stop and ask the orchestrator

### 2. Execute Narrowly

- Perform the smallest failing test or proof first when behavior changes
- Implement only the minimum required change
- Keep edits inside the owned files unless a task dependency truly requires a shared contract update

### 3. Verify

- Run the task's declared verification commands
- Capture the pass/fail result
- If verification fails outside the task boundary, report the blocker instead of expanding scope

### 4. Return

- List files changed
- List commands run
- List the final result and any blockers — enough detail for **`sdd-build`** to **`- [x]`** the task’s **Verification** lines in **`docs/plans/...`** when passes are real
- Hand control back to `sdd-build` (orchestrator applies plan-file checkbox updates unless the repo uses an external SSOT)

## Output Template

```markdown
**When handing work back to `sdd-build`:**

- `Owned files`: …
- `Files changed`: …
- `Commands run`: …
- `Verification result`: …
- `Blockers`: …
```

## Present Results to User

- Keep the return brief and evidence-first.
- State the owned files and the exact result.
- Make blockers explicit instead of implying progress.
- Hand control back to `sdd-build` without re-planning.

## Verification

- [ ] Ownership stayed narrow
- [ ] The evidence is concrete enough for the parent task
- [ ] The blocker state is explicit

## Red Flags

- Editing files outside ownership
- Inventing new task scope
- Re-planning from inside the subagent
- Returning vague progress without evidence

## Prompt Next Step

Say the next step outright (question optional): **`sdd-build`**.
