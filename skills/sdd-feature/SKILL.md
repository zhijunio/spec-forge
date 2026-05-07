---
name: sdd-feature
description: Freeze facts, requirements, and success criteria into docs/features/<feature-slug>.md for sdd-plan; implementation-free, reviewable, and stable.
---

# sdd-feature

This skill freezes one scoped feature into **`docs/features/<feature-slug>.md`** as implementation-free facts, requirements, and success criteria with stable short titles, then hands off to **`sdd-plan`**—it never writes plans or code.

## When to Use

- Freeze facts, requirements, and success criteria into one feature document (`sdd-brainstorm` optional).
- Scope supports concrete, observable requirements; next step is **`sdd-plan`**, not open-ended exploration.

## Process

### 0. Validate Inputs

- One-sentence **`feature_description`**. If missing, ask once and wait.
- **`PROJECT.md`** missing or too thin to anchor scope → stop until facts exist (**`sdd-init`**, hand-written **`PROJECT.md`**, or equivalent)—do not invent stack or conventions.
- Thin **`README.md`** alone is insufficient when stack, boundaries, or verification are unclear; bootstrap **`PROJECT.md`** or confirm explicitly.
- Missing **`sdd-brainstorm`** is fine. If still fuzzy after `feature_description`, ask—or suggest **`sdd-brainstorm`** only when exploration genuinely helps.

### 1. Read Project Context

Order (repo root; skip missing): **`PROJECT.md`** → **`AGENTS.md`** → **`CLAUDE.md`** → **`README.md`**.

From **`PROJECT.md`** (README if thin): stack/runtime, architecture/naming, dependencies, boundaries.

**AGENTS / CLAUDE:** style and phrasing only—not a substitute for **`PROJECT.md`** facts.

**Exploration handoff** when brainstorm-like output exists:

- Read it; carry direction, labeled assumptions, open questions—no broad re-discovery.
- Before **Write `docs/features/<feature-slug>.md`**, confirm: direction for this feature document; blockers vs. entries under **Open Questions**; user wants the file frozen.
- Back to **`sdd-brainstorm`** only if direction is unclear or scope shifted materially.

### 2. Restate the Feature

One sentence; confirm if it drifts.

**No reply:** Do not widen scope silently. **Critical** mismatch (product, user, success)—wait or re-ask. **Minor** wording—proceed and note under **Assumptions**.

### 3. Freeze Boundary and Non-goals

Boundary from context; explicit **Non-goals** so **`sdd-plan`** cannot creep scope quietly.

### 4. Write Requirements as Observable Behavior

- **Stable short title** per item (**`sdd-plan` Covers**). Prefer `### Title` or bold lead—one chunk per requirement.
- Observable behavior only—no paths, classes, tables, framework hooks, concrete routes/URLs.
- User-visible contracts OK in plain language (e.g. session refresh without re-login)—without fixing `/api/...` or type names.
- Vague goals (“faster”, “stable”) → measurable bounds or recovery behavior; unknowns → **Open Questions**.

### 5. Define Success Criteria

- For each requirement, state what “done” looks like in measurable or observable terms.
- Prefer behavior-focused success criteria over implementation details.
- If a criterion is not yet knowable, park it under **Open Questions** instead of guessing.

### 6. Capture Key Scenarios and Edge Cases

Happy paths, key alternatives, edges.

### 7. Record Assumptions and Open Questions

Optional numbered assumption ping **before** locking **Requirements**; merge corrections into **Assumptions**. No silent invention of project-level facts (**`PROJECT.md`** / user). Prefer **Open Questions** over guessing.

### 8. Write `docs/features/<feature-slug>.md`

**Slug:** kebab-case, feature-specific—not `latest` / `final`. Split scope → new slug/file.

Order: Purpose → Scope → Non-goals → Requirements → Success Criteria → Key Scenarios → Assumptions → Open Questions.

## Output Template

```markdown
## Artifacts

- `docs/features/<feature-slug>.md` matching **Write `docs/features/<feature-slug>.md`** (slug + section order under **Process**).

## Conversation

- One-line summary of what was frozen + path to the file.

## Exit

- **Verification** passes (see **Verification** in this skill).
```

## Red Flags

- Writing **`docs/plans/`**, embedding tasks, file lists, or execution order—the artifact is **`docs/features/<feature-slug>.md`** only.
- Skipping **Validate Inputs** or skipping **Read Project Context** order when those files exist.
- Pasting project-wide command matrices, full trees, style manuals, or global test policy into the feature spec (belongs in **`PROJECT.md`** + **plan**).
- Implementation IDs in **Requirements** or skipping observable behavior because work “feels small.”
- Vague requirements where concrete behavior or **Open Questions** would do.
- Raw chat or **`sdd-brainstorm`** transcripts instead of titled **Requirements**.
- Another **`sdd-brainstorm`** round after **user-confirmed** direction without real scope change.
- Splitting one feature across specs without reconciliation.

## Verification

Pre-handoff gate only—run after **Write `docs/features/<feature-slug>.md`**, before **`sdd-plan`**. Checklist is not duplicated inside **Process**.

- **Validate Inputs** satisfied; **Read Project Context** order followed when those files exist.
- **Restate the Feature** accurate or explicitly parked under **Assumptions** / **Open Questions**.
- **Requirements**: stable, behavior-worded titles (**Covers**-ready); no implementation names.
- **Scope** and **Non-goals** explicit.
- Path **`docs/features/<feature-slug>.md`** matches slug rules and the section order in **Write `docs/features/<feature-slug>.md`**.
- No plan content, task lists, or file-level execution steps inside the spec.

When all pass: one-line summary of what was frozen + path to the file; hand off to **`sdd-plan`** with the same `<feature-slug>`—do not draft a plan here.

## Prompt Next Step

Say next skill/path outright (question optional): **`sdd-plan`** with the same `<feature-slug>`.
