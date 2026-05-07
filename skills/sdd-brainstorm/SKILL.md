---
name: sdd-brainstorm
description: Refines vague ideas before `sdd-feature`. Use when the request is still fuzzy, exploratory, or needs divergent thinking before freezing a feature spec.
---

# sdd-brainstorm

Turn a vague idea into a clear direction worth taking into `sdd-feature`. Use this skill before `sdd-feature` when the user has a rough concept, a half-formed request, or multiple possible directions.

Do not invoke implementation skills, edit code, or run implementation actions until both are true:

- a recommended direction is presented (through Process below), and  
- the user confirms it (Transition gate).

Brainstorming ends in a spec handoff (`sdd-feature`), not coding. Do not create or edit files under `docs/features/` or `docs/plans/` in this skill—the structured summary stays in the conversation (see Output).

## When to Use

- The request is fuzzy or exploratory.
- The user wants brainstorming before committing to `sdd-feature`.
- The problem needs divergence, not implementation.
- You need to decide whether the idea is ready for `sdd-feature`.
- The request may span multiple independent pieces—decompose first and brainstorm the first slice.

## Process

Follow steps **1–10** below in order.

### 1. Explore project context

- Read project files in this order (repo root unless noted; skip missing), same as `sdd-init`, `sdd-feature`, and `sdd-plan`: `PROJECT.md`, `AGENTS.md`, `CLAUDE.md`, `README.md`.
- Anchor stack, layout, and commands from `PROJECT.md` when present; use `README.md` when project facts are thin. Use `AGENTS.md` and `CLAUDE.md` for style and hints—they do not replace manifests (`package.json`, etc.) or a solid `PROJECT.md`.
- When the idea touches an existing codebase, skim relevant files, docs, and recent changes; note patterns the design should respect.

### 2. Restate the idea as a crisp problem statement

### 3. Assess scope early

- If the request includes multiple independent subsystems, stop and decompose first.
- Brainstorm the first sub-problem instead of pretending one spec can cover everything.

### 4. Ask sharpening questions before proposing solution approaches

- Scope pass at most once: only if ambiguity remains after **Explore project context**. If already clear enough to freeze, skip. Otherwise send exactly one message with at most three numbered questions—never a second scope-pass batch.
- After that (or if skipped): ask only one blocking question per turn until you can compare options or the user picks a direction.
- Prefer multiple-choice when practical; use open-ended questions only when the space is still too unclear.
- If there was no scope pass, plan roughly three to five single-message rounds before listing distinct approaches—do not under-sharpen.
- Use Divergence Prompts when widening the problem space.
- Do not ask many questions at once unless the ambiguity truly blocks every viable direction; keep the conversation pointed at the smallest missing decision.
- If a question can be answered by exploring the codebase or docs, do that first instead of asking the user.

### 5. List known facts, constraints, and assumptions separately

- Label assumptions explicitly.

### 6. Identify the unknowns that materially affect the direction

### 7. Generate two or three distinct approaches

- Not variants of the same approach.

### 8. Compare the options

- On value, feasibility, scope, and risk (and fit with this repo).

### 9. Summarize the chosen direction

- Major pieces, boundaries, risks; use Mermaid only when it clarifies flow or ownership. Lead with the recommended approach and explain why it wins. Keep it short enough for one pass—clarity over cleverness.

### 10. Recommend one outcome

- Enter `sdd-feature` (default path), or
- Continue brainstorming if key ambiguity remains.

If the user revises the idea or ambiguity returns, revisit earlier steps as needed—do not pretend the direction is settled.

Suggested question areas:

- Who this is for
- What problem it solves
- What success looks like
- Real constraints and unknowns
- Why now

For the scope pass, pick at most three from the cues above plus: split vs single feature; what blocks a decision; what was already tried.

## Divergence Prompts

- Who is this for?
- What pain are we solving?
- What is the smallest useful version?
- What would make this fail?
- What constraints are non-negotiable?
- What options should we reject now?

## Convergence Criteria

Before treating the session as ready for `sdd-feature`:

- The user, problem, and desired outcome are clear enough to write acceptance criteria.
- The recommended direction is materially better than the alternates.
- The main risks are known, even if not fully solved.
- The next step is explicit: `sdd-feature` by default, or continued brainstorming if blocking ambiguity remains.
- The user has confirmed the recommended direction (or an alternate you presented).

## Output Template

```markdown
**Deliver in conversation only** (no repo files). Short markdown-shaped summary; becomes input to `sdd-feature`.

### Problem Statement

- …

### Candidate Directions

- …
- …

### Key Assumptions

- …

### Facts / Constraints

- …

### Risks / Unknowns

- …

### Recommended Direction

- …

### Why This Direction

- …

### Next Step

- `sdd-feature` or continued brainstorming.

### Open Questions

- …
```

Do not write the feature spec in the repository during this skill.

## Present Results to User

- Lead with the recommended direction or the blocking ambiguity.
- Keep the summary short enough to read in one pass.
- Mention only the facts, assumptions, and risks that changed the decision.
- End with the exact next step: `sdd-feature` or continue brainstorming.

## Red Flags

- Moving to **`sdd-feature`** while the problem or scope is still too vague.
- Any implementation or coding before **Transition gate** user confirmation.
- Skipping **Explore project context** when the idea touches an existing codebase.
- Skipping clarifying questions while the request is still vague.
- Question dumps: many unrelated questions at once or repeated multi-question scope batches (violates **Ask sharpening questions before proposing solution approaches**: one scope batch, ≤3 questions, once).
- Oversized idea lists or too many directions instead of two or three distinct approaches.
- Soft-pedaling weak ideas, chasing cleverness over clarity, or inventing requirements the user never stated.
- Long output that is not readable in one pass.
- Inferring stack or layout without reading project files in fixed order: **`PROJECT.md`**, **`AGENTS.md`**, **`CLAUDE.md`**, **`README.md`**.
- Ending without explicit user confirmation on direction and next step.
- Writing or editing **`docs/features/`** or **`docs/plans/`**, or treating brainstorm as spec/plan work.
- Treating a large multi-system initiative as one small feature without decomposition.
- Pretending the direction is settled when ambiguity remains.
- Hiding assumptions inside the recommendation instead of labeling them.

## Transition gate

Before exiting this skill:

1. Confirm the user approved the recommended direction.
2. Choose exactly one next step:
   - `sdd-feature` (default), or
   - Continue brainstorming if key ambiguity remains.

When choosing `sdd-feature`, carry the Output summary forward as context for the handoff.
