---
name: sdd-build
description: >
  Orchestrate execution of docs/plans/<yyyy-mm-dd-feature-slug>.md task-by-task:
  validate inputs, TDD-first for behavior changes, narrow context, optional
  delegation to sdd-subagent-build per isolated task. 
---

# sdd-build

Execute the plan. Do not rewrite requirements, re-plan the work, or perform formal review.

## When to Use

- You already have a plan and need to implement it
- The work is small enough to stay inside one execution loop
- You want TDD-first execution with fresh subagents for independent slices

## Core Rules

- Follow the plan order unless a dependency forces a safe reordering
- Keep changes inside the task's declared files and acceptance criteria
- Prefer a **fresh subagent** with **`sdd-subagent-build`** for **independent** tasks or lanes (narrow packet from the plan task)
- Keep context narrow; do not drag in unrelated feature, plan, or review work
- Preserve a minimal evidence chain: failing test, fix, passing test, optional refactor
- Use TDD for behavior changes: write the smallest failing test or proof first, then make it pass
- After each completed task, hand the result to `sdd-review` before moving on when the task is non-trivial or touches shared contracts
- If review recommends simplification, run `sdd-simplify` on an explicitly agreed scope (often the current task’s touched files), then re-`sdd-review` when the refactor is non-trivial before the next task

## Process

### 1. Validate Inputs

- `docs/plans/<yyyy-mm-dd-feature-slug>.md` exists
- The matching feature document exists if the plan references it
- `PROJECT.md` exists when the plan depends on repo facts
- If the task is underspecified, stop and ask for clarification

### 2. Read the Task

- Read the current task's `Covers`, `Acceptance criteria`, `Verification`, `Dependencies`, and `Files likely touched`
- Confirm the owned file set before editing anything
- If the task is independent and isolated, delegate it to a **fresh subagent** using **`sdd-subagent-build`** (pass only that task’s fields and allowed files)

### 3. Execute TDD

- Write the smallest failing test or proof first when the task is behavior-changing
- Confirm the failure
- Implement the minimum fix
- Confirm the test passes
- Refactor only if it stays within scope and keeps behavior stable

### 4. Keep the Boundary

- Do not expand scope into new requirements
- Do not rewrite the plan
- Do not replace review or simplify phases
- If you discover a plan defect, report it and stop or request a plan update

### 5. Finish Cleanly

- Summarize files changed
- Summarize commands run and outcomes
- Call out any skipped verification and why
- Leave the next task clearly identifiable
- If the task is ready, explicitly recommend `sdd-review`, `sdd-simplify`, or the next `sdd-build` task as appropriate
- Do not claim the task is complete until the verification command has been run in this session and the output supports the claim

## Output Template

```markdown
**When handing back a completed task:**

- `Task`: …
- `Files changed`: …
- `Commands run`: …
- `Verification result`: …
- `Next step`: …
```

## Present Results to User

- Start with what task was completed and whether it stayed within plan.
- State the verification result plainly.
- Call out any skipped checks and why.
- End with the next actionable step.

## Verification

- [ ] The task stayed within plan
- [ ] The evidence chain is complete enough for the next step
- [ ] The next action is explicit
- [ ] Any completion claim is backed by fresh verification evidence from this session

## Red Flags

- Implementing outside the plan
- Touching shared files without checking overlap
- Skipping the failing-test step for behavior work
- Treating build execution as a new planning phase

## Prompt Next Step

Say the next step outright (question optional): **`sdd-review`**, **`sdd-simplify`**, or the next **`sdd-build`** task.
