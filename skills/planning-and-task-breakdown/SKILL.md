---
name: planning-and-task-breakdown
description: >-
  Plan execution and break complex work into ordered, verifiable tasks aligned
  with `spec-driven-workflow`. Use when translating a spec into a concrete
  plan, scoping ambiguous work, or mapping dependencies. Do not use for tasks
  already broken down.
---

# Planning & Task Breakdown

Decompose specifications into clear execution steps (`workflow/<change-slug>/PLAN.md`) and, when warranted, small verifiable tasks (`workflow/<change-slug>/TASKS.md`) before writing any implementation code.

> **Language & Architecture Agnostic**: This skill applies to any technology stack, domain, or project structure. Reason in terms of deliverables, boundaries, dependencies, and verification—never assume specific languages, frameworks, or architectural patterns.

**Foundation**: Apply the KISS-first doctrine in `code-simplification`: KISS is the master philosophy, SOLID is a contextual heuristic for object-oriented design, and KISS/YAGNI win ties. Where SOLID applies, grade a deviation by concrete harm (testability, coupling, change amplification). The plan itself must be KISS-compliant (simplest viable path).

## Core Rule

**Plan in read-only mode.** Explore the repository, map dependencies, and write the plan before modifying any deliverable files. Do not start implementing while planning.

**Doctrine Planning Gate**: Before finalizing any plan, verify:
- Is this the simplest plan that achieves the spec goals? (KISS)
- Where SOLID applies, does the plan structure work to produce doctrine-compliant code? Grade deviations by concrete harm.
- Are there any speculative/premature steps? Remove them (YAGNI).

## When to Apply

- Translating `workflow/<change-slug>/SPEC.md` into an actionable execution plan (`workflow/<change-slug>/PLAN.md`).
- Work feels too large, ambiguous, or multi-faceted to execute in a single pass.
- Multiple independent deliverables or parallel/asynchronous agent executions are possible.
- Skip formal breakdown for trivial, single-step edits where planning adds no value.

## Planning Process

### 1. Ground in `workflow/<change-slug>/SPEC.md`

1. Read `workflow/<change-slug>/SPEC.md`.
   - If `SPEC.md` does not exist yet for a non-trivial change, create and align on `workflow/<change-slug>/SPEC.md` first.
2. Confirm goals, non-goals, constraints, and acceptance criteria are unambiguous. Surface open questions before committing to a plan.
3. **Doctrine Spec Review**: Does the spec itself favor simple solutions? Flag any requirement that would force a principle deviation to the Architect/Product Owner *before* planning.

### 2. Explore & Map Dependencies (Read-Only)

1. Inspect the relevant areas of the repository (preferring indexed context if `codebase-indexing` is active).
2. Identify affected components, shared boundaries, and external constraints.
3. Map the **dependency graph**: determine what blocks what, which steps must run sequentially, and which units are truly independent.
4. **Doctrine Boundary Check**: Identify existing module boundaries, interfaces, and abstraction layers. The plan must respect (not violate) these. Where SOLID applies, grade any deviation by concrete harm.

### 3. Author `workflow/<change-slug>/PLAN.md`

Create or update `workflow/<change-slug>/PLAN.md` (initializing `workflow/<change-slug>/` if missing):

- Reference `SPEC.md` requirements rather than duplicating them.
- **Order steps logically** — tackle high-risk or high-uncertainty unknowns early (Risk-First), but **keep each step KISS-compliant** (simplest probe, not elaborate scaffold).
- **Design for doctrine outcomes**: Each step should produce code with a single responsibility. Steps that create or modify interfaces should be explicit where SOLID applies. Steps should extend behavior via new code, not modify working code.
- Define a concrete **Verification Strategy** (automated checks, validations, or manual verification) for each step and for the overall change.
- Note risks and rollback considerations.
- **Explicitly mark doctrine decision points**: Where the plan makes a design choice that affects simplicity or doctrine compliance, document the rationale.

### 4. Shard into `workflow/<change-slug>/TASKS.md` (Conditional)

Create `workflow/<change-slug>/TASKS.md` **only** when at least one `spec-driven-workflow` trigger applies:

1. `PLAN.md` is too large or comprehensive to execute cleanly in a single pass.
2. `PLAN.md` spans two or more distinct scopes, task types (`feature`, `bugfix`, `refactor`, `docs`), or independent deliverables that benefit from separate tracking.
3. Work can be split into independent units executed **in parallel or asynchronously** by different agents or contributors.

**Task Design Heuristics** (when creating `TASKS.md`):

- **One responsibility per task**: Each task must have a single, clear responsibility. A task that does "auth + persistence + notifications" should be split.
- **Define contracts before implementations**: Tasks that implement concrete behavior should depend on interfaces defined in prior (or parallel) tasks.
- **Prefer additive tasks**: Tasks that *add* new files/modules are safer than tasks that *modify* existing working code.
- Apply the canonical doctrine from `code-simplification` (KISS master, SOLID contextual, grade by concrete harm) where a task touches design.

When creating `TASKS.md`, ensure every task has:

- **Unique ID & Type** (e.g., `T1`, `feature` | `bugfix` | `refactor` | `docs` | `chore`).
- **Explicit Dependencies**: Clearly state blocking task IDs (or `None`).
- **Disjoint Scope**: Assign non-overlapping files or modules to parallel tasks to prevent merge conflicts.
- **Objective Done Criteria**: Concrete, verifiable conditions that prove the task is complete — **including doctrine compliance checks** (see Quality Checklist).
- **Doctrine Notes** (optional but recommended): Brief note on how this task upholds simplicity and doctrine (e.g., "Implements `PaymentGateway` interface; no concrete deps").

## Quality Checklist

Before handing off `PLAN.md` (and `TASKS.md`) for execution:

1. Every requirement and acceptance criterion in `workflow/<change-slug>/SPEC.md` is covered by at least one step or task.
2. No circular dependencies exist, and parallel tasks have non-overlapping scopes.
3. Every step/task is small enough to implement and verify in a single focused pass.
4. No implementation code has been written prematurely during the planning phase.
5. **Doctrine Compliance**: The plan has no speculative steps, no premature abstractions, no "infrastructure first" horizontal layering unless the spec explicitly requires it. Where SOLID applies, grade any deviation by concrete harm per `code-simplification`.
6. **Vertical Slice Bias**: Plan defaults to vertical slices (end-to-end capability) over horizontal layers, unless risk-first or contract-first is explicitly justified.

## Anti-Patterns in Planning

| Anti-Pattern | Doctrine Violation | Correction |
|--------------|---------------------|------------|
| **Horizontal layer plan** (all models → all services → all controllers) | Delays integration; encourages anemic domain; violates vertical slice principle | Default to vertical slices |
| **"Infrastructure first" without consumer** | YAGNI (KISS); speculative generality | Build infrastructure only when a vertical slice needs it |
| **Fat tasks** (one task = entire feature) | Hard to verify; blocks parallelism | Decompose to single-responsibility tasks |
| **Concrete-first task ordering** | Concretions before abstractions | Define interfaces first; implement concretions after |
| **Modifying stable core for new feature** | Risk of regression | Extend via new modules/adapters; minimize core changes |
| **No verification strategy per step** | Unverifiable plan; quality gate bypassed | Every step needs explicit verification criteria |