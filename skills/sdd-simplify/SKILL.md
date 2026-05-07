---
name: sdd-simplify
description: >
  Simplifies code for clarity. Use when refactoring code for clarity without
  changing behavior. Use when code works but is harder to read, maintain, or
  extend than it should be. Use when reviewing code that has accumulated
  unnecessary complexity. Scope comes from an explicit user request or agreed
  area (paths, module, or change set)—not from requiring feature/plan docs or a
  prior review artifact.
---

# sdd-simplify

Simplify working code inside an **explicitly agreed simplify scope** only. Do not expand scope, reopen design, or change behavior. **`docs/features/`** and **`docs/plans/`** are optional context when they exist—they do **not** define where you may edit unless the user ties simplification to them.

Announce at start (match user-requested language): "I am using **sdd-simplify** to reduce complexity inside the agreed simplify scope only."

## When to Use

- After a feature is working and tests pass, but the implementation feels heavier than it needs to be
- During code review when readability or complexity issues are flagged
- When you encounter deeply nested logic, long functions, or unclear names
- When refactoring code written under time pressure
- When consolidating related logic scattered across files
- After merging changes that introduced duplication or inconsistency

**When NOT to use:**

- Code is already clean and readable
- You don't understand what the code does yet
- The code is performance-critical and a simpler version would be measurably slower
- You're about to rewrite the module entirely

## Five Principles

### 1. Preserve Behavior Exactly

- Don't change what the code does, only how it expresses it.
- If you're not sure a simplification preserves behavior, don't make it.

### 2. Follow Project Conventions

- Read `CLAUDE.md` / project conventions.
- Match neighboring code for style, naming, and error handling.

### 3. Prefer Clarity Over Cleverness

- Explicit code is better than compact code when the compact version requires a mental pause to parse.

### 4. Maintain Balance

- Avoid over-simplification.
- Do not remove abstractions that still earn their complexity.

### 5. Scope to What Changed

- Default to simplifying recently modified code.
- Avoid drive-by refactors of unrelated code unless explicitly asked.

## Core Rules

- Preserve behavior exactly unless the user explicitly approves a behavior change
- Stay inside the **agreed simplify scope** (explicit paths, module, task description, or current change set the user approved for simplification)—do **not** infer scope only from `feature` / `plan` documents
- Prefer small, reversible simplifications over broad refactors
- Remove complexity, not just lines
- Ask before deleting uncertain dead code
- Split refactoring from feature work
- When simplification is complete, hand the result back to `sdd-review` if the change was non-trivial or touched shared contracts

## Simplification Targets

Prioritize these in order:

1. Duplicate logic
2. Overly clever control flow
3. Dead or unreachable code
4. Unnecessary abstraction layers
5. Confusing names and local structure
6. Excess coupling or unclear boundaries

Only touch performance when the review already flagged it or the improvement is obvious and safe.

### 1. Duplicate Logic

- Merge repeated logic when the shared shape is already obvious.

### 2. Control Flow

- Flatten nested branches and remove unnecessary branching indirection.

### 3. Dead Code

- Remove unreachable paths, stale branches, and unused helpers when they are confirmed safe to delete.

### 4. Abstraction Layers

- Remove wrappers or generalizations that no longer earn their complexity.

### 5. Names and Structure

- Rename unclear locals, functions, or modules when the new name makes the intent easier to read.

### 6. Boundaries and Coupling

- Reduce unnecessary cross-file coupling and keep module boundaries easier to follow.

## Process

### 1. Confirm the Simplify Scope

- Take scope from the user's explicit request (paths, globs, module name, “this PR”, “files touched in the last change”) or from a narrow task they assigned for cleanup
- Confirm what may be edited and what must not move; optional: skim **`PROJECT.md`** / **`README.md`** for conventions—do **not** treat **`docs/features/`** or **`docs/plans/`** as mandatory inputs
- If the range is unclear, stop and ask for clarification
- If the change is more than one obvious cut, write a tiny refactor sketch first: what to cut, how to prove it, and what would make you stop

### Range Selection Rules

- **Prefer** the narrowest scope the user named; if they named nothing, **infer** from the current change set or last touched files *only when that inference is obviously safe*
- **Ask the user** when multiple scopes are plausible, the edit boundary would cross team-owned boundaries, or deleting a candidate would require guessing
- **Do not ask** just because the simplification is small; narrow is the default
- **Do ask** before expanding beyond the agreed simplify scope, even if the change seems obviously beneficial
- If scope is too vague to simplify safely, stop and ask—do **not** invent a boundary from **`feature`** / **`plan`** documents that the user did not invoke

### 2. Identify the Simplest Safe Cut

- Find the smallest change that reduces complexity
- Prefer one simplification at a time
- Keep the change easy to review and revert
- Use Chesterton's Fence before removing anything you do not understand
- If the change would touch more than 500 lines, use automation instead of hand-editing

### 3. Preserve Behavioral Proof

- Keep or update tests that prove behavior did not change
- If the simplification affects a public contract, verify the contract still holds
- Do not trade clarity for a more clever implementation

### 4. Remove Waste Carefully

- Delete dead code only when no live path depends on it
- If unsure, surface the candidate and ask before removing it
- Keep supporting names and structure readable for the next engineer or agent

### 5. Close with Evidence

- Summarize what got simpler
- Note what remained intentionally unchanged
- Call out any follow-up simplifications that were left out of scope
- Ask whether the user wants the simplification committed or re-reviewed

## Output Template

```markdown
**When closing a simplification pass:**

- `Simplify scope`: …
- `What got simpler`: …
- `What stayed unchanged`: …
- `Files changed`: …
- `Verification`: …
- `Follow-up`: …
```

## Verification

- [ ] The agreed simplify scope is respected
- [ ] Behavior is unchanged or explicitly approved
- [ ] The change reduced complexity in a measurable way
- [ ] Tests or proofs still cover the simplified path
- [ ] Any dead code removed was confirmed safe
- [ ] The simplification summary explains what became easier

## Red Flags

- Changing behavior under the banner of simplification
- Expanding simplify scope silently because `feature` / `plan` files exist or suggest a wider refactor
- Performing a large refactor when a smaller cut would do
- Deleting code without confirming it is truly unused
- Leaving the code functionally correct but equally hard to understand

## Prompt Next Step

Say the next step outright (question optional): **`sdd-review`**.
