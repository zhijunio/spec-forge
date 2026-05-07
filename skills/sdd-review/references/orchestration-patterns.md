# Orchestration Patterns Reference

Companion to the SDD skill set in this repo. Catalog of orchestration patterns and anti-patterns. Read this before adding a new routing layer or a persona that wraps other personas. For **code review** delivery shape and subagent use, see **`skills/sdd-review/SKILL.md`** and **`agents/code-reviewer.md`**.

## Core Rule

- The user or main agent is the orchestrator.
- Personas should not invoke other personas.
- Skills are mandatory steps inside a persona workflow.

## Pattern 1: Direct Invocation

- One persona
- One artifact
- One perspective
- Use when the task is narrow and can be answered directly
- Cheapest option; compare against this first

Example:

- "Review this PR" -> one review persona
- "Find security issues in auth.ts" -> one security persona

## Pattern 2: Single-Persona Routed Workflow

- The main agent routes to one persona plus its skill
- Good when the same setup repeats often
- Bad if the routing layer mostly just chooses a persona

Use when:

- the workflow is stable
- the setup is tedious to repeat
- the routing adds a real default, not a router layer

## Pattern 3: Parallel Fan-Out with Merge

- Multiple personas inspect the same input in parallel
- Each returns a distinct report
- The main agent merges the reports into a decision

Use when:

- subtasks are independent
- each persona benefits from its own context window
- the merge fits in the main context
- parallelism actually saves wall-clock time

Validation before adopting:

- Can the subagents run at the same time?
- Do they produce different kinds of findings?
- Can the merge fit in the main context?
- Is the wait long enough that parallelism matters?

Example:

```text
ship intent -> code-reviewer + security-auditor + test-engineer -> merge -> go/no-go
```

## Pattern 4: Sequential User-Driven Lifecycle

- The user runs phases in order
- Each phase depends on the previous output
- Human judgment stays in the loop

Use when:

- requirements are still being shaped
- each phase produces an artifact the next phase needs
- automatic orchestration would hide important decisions

## Pattern 5: Research Isolation

- Spawn a read-only research agent
- Keep large investigations out of the main context
- Return a digest, not a dump

Use when:

- the input is large
- the result is much smaller than the source material
- the main session needs room to reason after reading

Example:

```text
main agent -> research subagent -> short digest -> main agent continues
```

## Tooling notes (multi-agent / IDE)

- Subagents suit independent reports with separate context windows.
- Multi-agent or team flows fit when investigators should challenge each other — still merge in the orchestrator.
- Do not build a router persona that only picks another persona.
- Do not nest persona trees.

## Decision Checklist

- Is this one perspective or many?
- Are the subtasks independent?
- Does the merge fit in one turn?
- Would a routing layer add value, or only extra indirection?

## Anti-Patterns

- Router persona that only decides which persona to call
- Persona that calls another persona
- Orchestrator that runs the whole lifecycle for the user
- Deep persona trees with multiple coordination layers
- Sequential orchestration hidden inside a persona

## Choose the Right Shape

- One perspective on one artifact -> direct invocation
- Repeated single-persona workflow -> routed persona workflow
- Independent analyses that need merge -> parallel fan-out
- Ordered phases with human judgment -> user-driven lifecycle
- Large research with small summary -> research isolation
