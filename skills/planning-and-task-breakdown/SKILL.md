---
name: planning-and-task-breakdown
description: >-
  Plan execution and break complex work into ordered, verifiable tasks aligned
  with `spec-driven-workflow` (`workflow/SPEC.md`, `workflow/PLAN.md`, and
  conditional `workflow/TASKS.md`). Use this skill when translating a
  specification into a concrete execution plan, scoping large or ambiguous work,
  mapping dependencies, or preparing parallelizable tasks.
---

# Planning & Task Breakdown

Decompose specifications into clear execution steps (`workflow/PLAN.md`) and, when warranted, small verifiable tasks (`workflow/TASKS.md`) before writing any implementation code.

> **Language & Architecture Agnostic**: This skill applies to any technology stack, domain, or project structure. Reason in terms of deliverables, boundaries, dependencies, and verification—never assume specific languages, frameworks, or architectural patterns.

## Core Rule

**Plan in read-only mode.** Explore the repository, map dependencies, and write the plan before modifying any deliverable files. Do not start implementing while planning.

## When to Apply

- Translating `workflow/SPEC.md` into an actionable execution plan (`workflow/PLAN.md`).
- Work feels too large, ambiguous, or multi-faceted to execute in a single pass.
- Multiple independent deliverables or parallel/asynchronous agent executions are possible.
- Skip formal breakdown for trivial, single-step edits where planning adds no value.

## Planning Process

### 1. Ground in `workflow/SPEC.md`

1. Read `workflow/SPEC.md` (or `workflow/<change-slug>/SPEC.md` for scoped initiatives).
   - If `SPEC.md` does not exist yet for a non-trivial change, create and align on `SPEC.md` first.
2. Confirm goals, non-goals, constraints, and acceptance criteria are unambiguous. Surface open questions before committing to a plan.

### 2. Explore & Map Dependencies (Read-Only)

1. Inspect the relevant areas of the repository (preferring indexed context if `codebase-indexing` is active).
2. Identify affected components, shared boundaries, and external constraints.
3. Map the **dependency graph**: determine what blocks what, which steps must run sequentially, and which units are truly independent.

### 3. Author `workflow/PLAN.md`

Create or update `workflow/PLAN.md` (initializing `workflow/` if missing):

- Reference `SPEC.md` requirements rather than duplicating them.
- Order steps logically—tackle high-risk or high-uncertainty unknowns early.
- Define a concrete **Verification Strategy** (automated checks, validations, or manual verification) for each step and for the overall change.
- Note risks and rollback considerations.

### 4. Shard into `workflow/TASKS.md` (Conditional)

Create `workflow/TASKS.md` **only** when at least one `spec-driven-workflow` trigger applies:

1. `PLAN.md` is too large or comprehensive to execute cleanly in a single pass.
2. `PLAN.md` spans two or more distinct scopes, task types (`feature`, `bugfix`, `refactor`, `docs`), or independent deliverables that benefit from separate tracking.
3. Work can be split into independent units executed **in parallel or asynchronously** by different agents or contributors.

When creating `TASKS.md`, ensure every task has:

- **Unique ID & Type** (e.g., `T1`, `feature` | `bugfix` | `refactor` | `docs` | `chore`).
- **Explicit Dependencies**: Clearly state blocking task IDs (or `None`).
- **Disjoint Scope**: Assign non-overlapping files or modules to parallel tasks to prevent merge conflicts.
- **Objective Done Criteria**: Concrete, verifiable conditions that prove the task is complete.

## Quality Checklist

Before handing off `PLAN.md` (and `TASKS.md`) for execution:

1. Every requirement and acceptance criterion in `workflow/SPEC.md` is covered by at least one step or task.
2. No circular dependencies exist, and parallel tasks have non-overlapping scopes.
3. Every step/task is small enough to implement and verify in a single focused pass.
4. No implementation code has been written prematurely during the planning phase.
