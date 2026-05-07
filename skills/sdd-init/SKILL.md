---
name: sdd-init
description: >
  Build or refresh PROJECT.md from the repo (optional skill; facts may come from hand edits too).
  Downstream SDD needs a solid project context file. Use when context is missing or stale.
  Deterministic output: same inputs → same section order and field order.
---

# sdd-init

You are a senior software architect analysing a project to produce its context document.

Announce at start (match user-requested language): "I am using **sdd-init** to write or refresh **`PROJECT.md`** only; no feature or plan documents."

## When to Use

- New project context needs to be established.
- `PROJECT.md` is missing, stale, or too vague for downstream SDD stages.
- A factual baseline is needed before later SDD steps.

## Process

Order: **0** Check → **1** Facts → **2** Architecture → **3** Questions (if needed) → **4** Write → **5** Confirm.

### 0. Check PROJECT.md

- If `PROJECT.md` exists, ask whether to regenerate before proceeding.
- **Yes** → consent to **directly overwrite** in **4. Write**: one new file from repository scan + answers; **no** merge with old text.
- **No** → stop; tell them to edit manually.
- Missing file → proceed (new file from scratch in **4. Write**).

### 1. Detect the Project Facts

**Read first** (skip missing): `PROJECT.md` → `README.md`. Existing `PROJECT.md` is **optional context** during the scan; **stack and commands** come from manifests first.

Auto-detect everything the repo states clearly. Only ask about what cannot be confirmed from files.

Then scan the repo root and source tree:

| What to find        | Where to look |
|---------------------| --- |
| Tech Stack          | `package.json`, `pnpm-workspace.yaml`, `Cargo.toml`, `go.mod`, `pyproject.toml`, `pom.xml`, `build.gradle`, `build.gradle.kts` |
| Entry / deploy      | `Makefile`, `Taskfile.yml`, `docker-compose.yml`, `Dockerfile` |
| Config / layout     | `.env.example`, `config/`, `settings/`, `src/`, `app/`, `lib/`, `cmd/` |
| Docs / governance   | `README.md`, `docs/`, `ADR/`, `RFC/`, `CHANGELOG.md`, `CONTRIBUTING.md` |
| Repo-specific entry | Wrappers, bootstrap scripts, custom CLIs |

If this is an SDD / workflow / skill repo, say so—do not force an app-shaped story.

### 2. Detect Architecture & Conventions

Infer from the source tree (adjust or skip signals that do not apply):

- Identify the top-level source layout, such as `src/main/java`, `src/`, `app/`, or `lib/`.
- Infer architecture pattern from package or directory names when relevant:
    - `controller` / `service` / `repository` → Layered
    - `domain` / `application` / `infrastructure` / `adapter` → Hexagonal / Clean
    - Business modules (`order`, `payment`, …) each with layers → Modular monolith
    - Several services with separate builds → Microservices
- Identify the base package name, such as `com.example.myapp`.
- Look for a centralized error handler, such as `GlobalExceptionHandler`, middleware, error boundary, or equivalent.
- Look for a base REST path prefix in controllers or config, such as `/api/v1`.
- Note any security config, such as `SecurityConfig`, auth annotations, or JWT setup.
- Note any testing conventions that are obvious from the source tree.
- Note repository-level documentation patterns, such as ADRs, RFCs, or CHANGELOG-style files.
- Note monorepo indicators, such as workspaces, `nx.json`, `lerna.json`, or `turbo.json`.
- Workflow / skill / template repos: treat `skills/` and `docs/` as structural signals.

If monorepo: describe from repo root; nest packages/apps under that context unless they need separate SDD cycles.

### 3. Ask Clarifying Questions

When facts are uncertain, ask in **one** numbered message; scope = project facts only.

- Ask **at most once per run** unless the user explicitly changes the facts.
- Group related uncertainties into a single message.
- If a single yes/no answer would unblock the write, ask only that one question.
- Do not ask about facts the repo already answers clearly.

**Skip the questionnaire** when **all** are true—go straight to **4. Write** and park unknowns in **Open Questions**:

- Mission is clear from **README** and/or existing `PROJECT.md` (README alone is enough when `PROJECT.md` is absent)
- Stack is obvious from manifests; no conflicting toolchains
- At least one **Build** or **Test** command is discoverable (or honestly `none` for doc-only repos)
- Nothing major unknown would change **Dependency Policy** or the architecture label

If **any** fails, use **shorten** or the **full template** below.

If a fact can be derived from the repo with high confidence, treat it as auto-detected and write it directly instead of asking.

**Shorten:** one confirm-only line when a single dimension is weak.

**Otherwise:** full template:

```text
I've analysed the project. Before I write PROJECT.md, I need a few details:

1. Project name and mission: What does this repo do, and who uses or maintains it?
   (1–2 sentences—your "why we exist". For an application: product + audience. For a workflow/skill/template/docs-only repo: what it provides and who it is for—not forced into an app story.)

2. Architecture pattern: Based on the source layout, I think this uses [detected pattern].
   Is that right, or would you describe it differently?

3. Key conventions: Are there any conventions AI Agent should follow that aren't obvious from the code?
   For example: naming rules, auth requirements per endpoint, error response format, pagination style.

4. Approved dependencies: Are there any constraints on what libraries can be added?
   (If not, I'll list the detected dependencies as approved by default.)
```

Wait for answers before writing (unless a single yes/no is already answered inline).

### 4. Write PROJECT.md

Fill the template from analysis + answers. Deterministic section/field order: Mission → Project Facts (subsections and bullets below in fixed order) → remaining top-level sections. Use `none` for empty bullets; **do not** skip a subsection—keep headings so downstream tools can scan predictably. Keep this document factual and repository-scoped (no feature requirements/tasks). **Overwrite:** only full replace per **0. Check** (yes to regenerate) or new file—never patch old content in place.

Target shape for **`PROJECT.md`**. The example below uses a **4-backtick** outer fence only so this SKILL file parses; when **you** write `PROJECT.md`, use **normal** fenced `bash` blocks for commands (3 backticks).

````markdown
# Project: <Name>

## Mission

<One paragraph: what this repo is for and who uses or maintains it.>

## Project Facts

### Classification

- Repo type: <Application | Workflow/Skill Pack | Template | Documentation | Library | Monorepo>

### Evidence (sources of truth)

- Manifests consulted: <paths like package.json / Cargo.toml / …, or "none">
- Docs consulted: <README, docs/*, skills/*, or "none">
- Maintainer-confirmed: <bullets, or "none">
- Uncertain / needs confirmation: <bullets, or "none">

### Technical baseline

- Tech stack: <languages, main frameworks with version>
- Configuration: <important config files or "none">
- Build & Test tool: <detected tools or "none">
- Deployment: <how it runs or ships, or "none">
- Data Store: <database/cache/queues/files of record, or "none">

## Architecture

<Best-fit pattern and repository boundaries in 2-4 sentences.>

## Source Layout

Indicate top-level layout (tree or bullets). Example shape:

```text
.
├── PROJECT.md
├── docs/
│   ├── features/
│   └── plans/
└── skills/
    └── sdd-init/
```

## Commands

Include only commands that exist in the repository. Omit the entire **Commands** section when there is no build/test/start/lint command to record (typical docs-only or skill-only repos).

### Build

```bash
<command, or "none">
```

### Test

```bash
<command, or "none">
```

### Start

```bash
<command, or "none">
```

### Lint

```bash
<command, or "none">
```

## Conventions

- Naming: <module, file, or package naming convention>
- Documentation: <e.g. uppercase PROJECT.md; features/plans stay in docs/>
- Error handling: <mechanism, or "none">
- Authentication/Security: <default requirement, or "none">
- SDD workflow notes: <constraints that affect sdd-feature/sdd-plan/sdd-build, or "none">

## Dependency Policy

- Runtime approved: <libraries/platform dependencies approved by default>
- Dev/build approved: <testing/lint/build tooling approved by default>
- Requires explicit approval: <heavy/risky/new external dependencies>

## External Services / Interfaces

<List external APIs, services, integration boundaries, or "none">

## Open Questions

<List unknowns that still matter for downstream SDD, or "none">
````

After writing `PROJECT.md`:

- If no explicit dependency constraints were provided, fill **Dependency Policy** from detected manifests and classify uncertain items under **Requires explicit approval**.

### 5. Confirm

After writing the file:

- Show the user a summary of what was detected automatically versus what they provided or assumed.
- Tell them to review `PROJECT.md` and update anything that looks wrong.
- Remind them to keep this file up to date as the stack evolves - it is the primary input for all other SDD skills.
- Prompt them to create the first feature under `docs/features/` when relevant.

## Output Template

```markdown
## Artifacts

- `PROJECT.md` at repository root (sections per **4. Write** in this skill).

## Conversation

- **Auto-detected:** …
- **User-provided or assumed:** …
- **Ask the user** to review `PROJECT.md` and keep it current as the stack evolves.
```

## Present Results to User

- Lead with whether `PROJECT.md` was created or refreshed.
- Call out the biggest auto-detected facts and the biggest assumptions.
- Mention any open questions that still matter for downstream skills.
- Ask the user to review the file before continuing with `sdd-feature`.

## Red Flags

- Overwriting **`PROJECT.md`** without explicit **yes** to regenerate (**Check PROJECT.md**).
- Shipping placeholder tokens instead of real values or omitted lines.
- Sending the **full** questionnaire when **Ask Clarifying Questions** should be skipped—or skipping it when the checklist is **not** satisfied.
- Guessing stack/runtime instead of deriving from manifests (`package.json`, `Cargo.toml`, etc.).
- Writing requirements, tasks, or implementation plans in this skill or stuffing them into **`PROJECT.md`**.
- Hiding uncertainty—residual unknowns must appear under **`PROJECT.md` Open Questions**.
- **Dependency Policy** missing split between runtime and dev/build, or approving risky dependencies without explicit user confirmation.
- Non-deterministic **`PROJECT.md`** shape: wrong section/field order; thin **Project Facts** (missing **Classification**, skipping **Evidence** when provenance matters, or empty **Technical baseline** without `none`); listing **Commands** that are not actually present in the repo.

## Prompt Next Step

Say the next step outright (skill/path); question optional.

- Continue with `sdd-feature`, or run `sdd-brainstorm` first if scope is still unclear.
