---
name: planning-and-task-breakdown
description: >-
  Plan execution and break complex work into ordered, verifiable tasks aligned
  with `spec-driven-workflow` (`workflow/<change-slug>/SPEC.md`, `PLAN.md`, and
  conditional `TASKS.md`). Use this skill when translating a specification into
  a concrete execution plan, scoping large or ambiguous work, mapping
  dependencies, or preparing parallelizable tasks.
---

# Planning & Task Breakdown

Decompose specifications into clear execution steps (`workflow/<change-slug>/PLAN.md`) and, when warranted, small verifiable tasks (`workflow/<change-slug>/TASKS.md`) before writing any implementation code.

> **Language & Architecture Agnostic**: This skill applies to any technology stack, domain, or project structure. Reason in terms of deliverables, boundaries, dependencies, and verification—never assume specific languages, frameworks, or architectural patterns.

**Foundation**: This skill operates under the **`code-simplification` foundational principles** — **KISS (Keep It Simple, Stupid)** and **SOLID**. The plan itself must be KISS-compliant (simplest viable path) and must structure work to produce SOLID-compliant code. Planning is where KISS/SOLID compliance is *designed in*, not inspected in.

## Core Rule

**Plan in read-only mode.** Explore the repository, map dependencies, and write the plan before modifying any deliverable files. Do not start implementing while planning.

**KISS/SOLID Planning Gate**: Before finalizing any plan, verify:
- Is this the simplest plan that achieves the spec goals? (KISS)
- Does the plan structure work to produce SOLID-compliant code? (SRP per task, DIP via interfaces, etc.)
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
3. **KISS/SOLID Spec Review**: Does the spec itself favor simple solutions? Are there requirements that would force SOLID violations? Flag these to the Architect/Product Owner *before* planning.

### 2. Explore & Map Dependencies (Read-Only)

1. Inspect the relevant areas of the repository (preferring indexed context if `codebase-indexing` is active).
2. Identify affected components, shared boundaries, and external constraints.
3. Map the **dependency graph**: determine what blocks what, which steps must run sequentially, and which units are truly independent.
4. **SOLID Boundary Check**: Identify existing module boundaries, interfaces, and abstraction layers. The plan must respect (not violate) these. Note where new abstractions are needed for DIP compliance.

### 3. Author `workflow/<change-slug>/PLAN.md`

Create or update `workflow/<change-slug>/PLAN.md` (initializing `workflow/<change-slug>/` if missing):

- Reference `SPEC.md` requirements rather than duplicating them.
- **Order steps logically** — tackle high-risk or high-uncertainty unknowns early (Risk-First), but **keep each step KISS-compliant** (simplest probe, not elaborate scaffold).
- **Design for SOLID outcomes**: Each step should produce code with a single responsibility (SRP). Steps that create or modify interfaces should be explicit (DIP/ISP). Steps should extend behavior via new code, not modify working code (OCP).
- Define a concrete **Verification Strategy** (automated checks, validations, or manual verification) for each step and for the overall change.
- Note risks and rollback considerations.
- **Explicitly mark KISS/SOLID decision points**: Where the plan makes a design choice that affects simplicity or SOLID compliance, document the rationale.

### 4. Shard into `workflow/<change-slug>/TASKS.md` (Conditional)

Create `workflow/<change-slug>/TASKS.md` **only** when at least one `spec-driven-workflow` trigger applies:

1. `PLAN.md` is too large or comprehensive to execute cleanly in a single pass.
2. `PLAN.md` spans two or more distinct scopes, task types (`feature`, `bugfix`, `refactor`, `docs`), or independent deliverables that benefit from separate tracking.
3. Work can be split into independent units executed **in parallel or asynchronously** by different agents or contributors.

**KISS/SOLID Task Design Principles** (when creating `TASKS.md`):

- **SRP per Task**: Each task must have a single, clear responsibility. A task that does "auth + persistence + notifications" violates SRP — split it.
- **DIP-Ready Tasks**: Tasks that implement concrete behavior should depend on interfaces defined in prior (or parallel) tasks. Sequence tasks so abstractions exist before concretions that depend on them.
- **ISP-Respecting Scope**: Don't force a task to implement a fat interface. If an interface is too large, split the interface *first* (separate task), then implement segregated parts.
- **OCP-Aware Sequencing**: Prefer tasks that *add* new files/modules over tasks that *modify* existing working code. When modification is needed, isolate it.
- **LSP-Safe Subtyping**: If a task creates a subtype, verify the base contract is respected. Document behavioral expectations.

When creating `TASKS.md`, ensure every task has:

- **Unique ID & Type** (e.g., `T1`, `feature` | `bugfix` | `refactor` | `docs` | `chore`).
- **Explicit Dependencies**: Clearly state blocking task IDs (or `None`).
- **Disjoint Scope**: Assign non-overlapping files or modules to parallel tasks to prevent merge conflicts.
- **Objective Done Criteria**: Concrete, verifiable conditions that prove the task is complete — **including KISS/SOLID compliance checks** (see Quality Checklist).
- **KISS/SOLID Notes** (optional but recommended): Brief note on how this task upholds simplicity and SOLID (e.g., "Implements `PaymentGateway` interface — DIP compliant; no concrete deps").

## Quality Checklist

Before handing off `PLAN.md` (and `TASKS.md`) for execution:

1. Every requirement and acceptance criterion in `workflow/<change-slug>/SPEC.md` is covered by at least one step or task.
2. No circular dependencies exist, and parallel tasks have non-overlapping scopes.
3. Every step/task is small enough to implement and verify in a single focused pass.
4. No implementation code has been written prematurely during the planning phase.
5. **KISS Compliance**: The plan has no speculative steps, no premature abstractions, no "infrastructure first" horizontal layering unless the spec explicitly requires it. The simplest viable path is chosen.
6. **SOLID Compliance**: 
   - **SRP**: Each task/step has one clear responsibility.
   - **OCP**: Plan favors extension over modification of existing working code.
   - **LSP**: Any subtyping in the plan respects base contracts.
   - **ISP**: Tasks don't force implementation of unused interface methods; interfaces are segregated.
   - **DIP**: Task dependency order ensures abstractions exist before concretions that depend on them.
7. **Vertical Slice Bias**: Plan defaults to vertical slices (end-to-end capability) over horizontal layers, unless risk-first or contract-first is explicitly justified.

## Anti-Patterns in Planning

| Anti-Pattern | KISS/SOLID Violation | Correction |
|--------------|---------------------|------------|
| **Horizontal layer plan** (all models → all services → all controllers) | Delays integration; encourages anemic domain; violates vertical slice principle | Default to vertical slices |
| **"Infrastructure first" without consumer** | YAGNI (KISS); speculative generality | Build infrastructure only when a vertical slice needs it |
| **Fat tasks** (one task = entire feature) | SRP violation; hard to verify; blocks parallelism | Decompose to single-responsibility tasks |
| **Concrete-first task ordering** | DIP violation; concretions before abstractions | Define interfaces first; implement concretions after |
| **Modifying stable core for new feature** | OCP violation; risk of regression | Extend via new modules/adapters; minimize core changes |
| **No verification strategy per step** | Unverifiable plan; quality gate bypassed | Every step needs explicit verification criteria |