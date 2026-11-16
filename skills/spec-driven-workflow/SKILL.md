---
name: spec-driven-workflow
description: >-
  Default software engineering workflow using a `workflow/` directory where
  `workflow/BACKLOG.md` is the only root file and every change lives in its own
  scoped subdirectory `workflow/<change-slug>/` (`SPEC.md`, `PLAN.md`,
  `TASKS.md`, `REVIEW.md`, and conditional `VALIDATION.md`). Use this skill as
  the standard process whenever starting a feature, bug fix, refactoring,
  architecture change, or code review—to capture product intent, specify
  requirements, plan execution, coordinate parallel work, track review
  findings, and validate delivery.
---

# Spec-Driven Workflow

Adopt this workflow as the **standard software engineering process** for any non-trivial change. Do not jump straight into writing code; follow the gated lifecycle below using the `workflow/` directory to separate **why/value** (`BACKLOG.md`), **what** needs to change (`SPEC.md`), **how** it will be built (`PLAN.md`), **who** executes independent pieces (`TASKS.md`), **what** needs attention after implementation (`REVIEW.md`), and **whether** the delivered value meets product expectations (`VALIDATION.md`).

## Standard Operating Flow

Follow these phases in order:

0. **Discover & Frame (`BACKLOG.md`, conditional)** — Capture product intent, user value, priority, or a prioritized mini-backlog of evolutions/bugs (typically owned by the `product-owner` role) before technical specification.
1. **Specify (`SPEC.md`)** — Define the problem, scope, constraints, and acceptance criteria (consuming `BACKLOG.md` when present) before designing or coding.
2. **Plan (`PLAN.md`)** — Map the technical approach, affected files, ordered steps, and verification strategy.
3. **Shard (`TASKS.md`, conditional)** — Break the plan into trackable tasks only when complexity, multi-scope work, or parallel execution warrants it.
4. **Implement** — Execute against `PLAN.md` (or `TASKS.md`), keeping artifact statuses current as work progresses.
5. **Review (`REVIEW.md`)** — Record code review findings (human or agent) and resolve blockers/major issues before completion.
6. **Validate (`VALIDATION.md`, conditional)** — Hand the approved delivery back to the Product Owner (or stakeholder) with plain-language summary and validation scenarios to confirm product value before cleanup.

> **Trivial changes exception**: Skip `workflow/` artifacts only for trivial, self-contained edits (e.g., typo fixes, single-line formatting, or minor comment updates) where specification and planning add no value.

## Directory Layout

Keep **`workflow/BACKLOG.md` as the only file at the `workflow/` root**. Always place all implementation-related artifacts (`SPEC.md`, `PLAN.md`, `TASKS.md`, `REVIEW.md`, `VALIDATION.md`, and change-specific assets) inside a dedicated subdirectory `workflow/<change-slug>/` named with a concise kebab-case slug identifying the feature, bug fix, refactor, or backlog item (e.g., `workflow/b1-checkout-fix/`, `workflow/user-authentication/`):

```text
workflow/
├── BACKLOG.md                # Optional: Only file at workflow/ root (Product Owner backlog & Ready briefs)
├── assets/                   # Optional: Backlog-level screenshots, mockups, or supporting evidence
└── <change-slug>/            # Required per non-trivial change (e.g., b1-checkout-fix, user-auth)
    ├── assets/               # Optional: Change-specific screenshots, diagrams, logs, and evidence
    ├── SPEC.md               # Required for any non-trivial change
    ├── PLAN.md               # Required before implementation starts
    ├── TASKS.md              # Optional: only for large plans or parallel/async agent work
    ├── REVIEW.md             # Created during human or agent code review
    └── VALIDATION.md         # Optional: Architect-to-PO delivery handoff and value sign-off
```

- **Why `<change-slug>/` is mandatory**: Isolating every initiative in its own `workflow/<change-slug>/` directory allows multiple agents to work in parallel on different plans (as long as their code scopes do not conflict), and allows an Architect to specify and plan upcoming items in `workflow/<slug-b>/` while a Worker implements an already-approved plan in `workflow/<slug-a>/`.
- **Never place `SPEC.md`, `PLAN.md`, `TASKS.md`, `REVIEW.md`, or `VALIDATION.md` directly in `workflow/` root.**

If `workflow/`, `workflow/<change-slug>/`, or any required artifact does not exist yet, **create the directory and initialize the missing files automatically** using the templates in `./resources/`.

### Supporting Assets & Evidence

Any agent or contributor in **any phase** (`BACKLOG.md`, `SPEC.md`, `PLAN.md`, `TASKS.md`, `REVIEW.md`, or `VALIDATION.md`) may attach screenshots (e.g., annotated UI bug captures), diagrams, logs, or other supporting files to enrich context for downstream or peer agents:

- **Storage location**:
  - Store backlog-level evidence referenced by `workflow/BACKLOG.md` in `workflow/assets/`.
  - Store change-specific evidence referenced by `SPEC.md`, `PLAN.md`, `TASKS.md`, `REVIEW.md`, or `VALIDATION.md` in `workflow/<change-slug>/assets/`.
  - Never scatter temporary evidence files in the repository root, source directories, or tracked `docs/` folders.
- **Naming**: Use clear, descriptive filenames (preferably kebab-case, prefixed with the related item/finding ID when applicable, such as `b1-checkout-bug-annotated.png` or `r2-mobile-overflow.png`).
- **Linking**: Reference attached files from any Markdown document using relative links or image embeds (e.g., `![Annotated checkout bug](./assets/b1-checkout-bug-annotated.png)` or `[Failure log](./assets/test-failure.log)`).

### Version Control

The `workflow/` directory—including `workflow/assets/` and all subdirectories—contains local process-management and guidance artifacts and **must never be tracked by Git**:

- Check whether `workflow/` is listed in the repository's `.gitignore`; if it is missing, ask the user for confirmation before adding it.
- Never stage or commit `workflow/` or any files inside it. If `workflow/` is somehow already tracked, warn the user before untrack/cleanup operations.

## Artifact Lifecycle

### 0. `workflow/BACKLOG.md` — Capture Product Intent & Backlog (Conditional)

Create or update `BACKLOG.md` when a Product Owner (or stakeholder) wants to frame a new feature, improvement, or bug fix—or maintain a prioritized mini-backlog of software evolutions—before engineering specification begins.

- **Purpose**: Separate product discovery, priority, and user value (*why* and *who*) from technical design (`SPEC.md`).
- **Template**: Start from [BACKLOG.md](./resources/BACKLOG.md).
- **Rules**:
  - Maintain an ordered **Product Backlog** table for candidate evolutions, features, and bug reports (`Idea`, `Refining`, `Ready`, `In Downstream`, `Done`).
  - Complete the **Active Demand Brief** and **Definition of Ready (DoR)** before handing an item off to `SPEC.md`.
  - Keep `BACKLOG.md` free of technical implementation details (file paths, schemas, or code).

### 1. `workflow/<change-slug>/SPEC.md` — Define the Change

Create `workflow/<change-slug>/SPEC.md` first for any new feature, bug fix, or architectural change.

- **Purpose**: Align on scope, behavior, constraints, and acceptance criteria before writing code.
- **Template**: Start from [SPEC.md](./resources/SPEC.md).
- **Rules**:
  - State both **Goals** and **Non-Goals** to prevent scope creep.
  - Write testable **Acceptance Criteria** so completion is objective.
  - For bug fixes, document reproduction steps, expected vs. actual behavior, and root cause (once known).

### 2. `workflow/<change-slug>/PLAN.md` — Map Execution Steps

Create `workflow/<change-slug>/PLAN.md` once `SPEC.md` is clear.

- **Purpose**: Translate the specification into concrete, ordered engineering steps.
- **Template**: Start from [PLAN.md](./resources/PLAN.md).
- **Rules**:
  - Reference `SPEC.md` requirements; do not duplicate the full spec.
  - List affected files/modules, ordered implementation steps, and a concrete verification strategy (tests, linters, manual checks).
  - Highlight risks, migrations, or rollback considerations if applicable.

### 3. `workflow/<change-slug>/TASKS.md` — Shard Large or Parallel Work (Conditional)

Create `workflow/<change-slug>/TASKS.md` **only** when at least one of these conditions holds:

1. The `PLAN.md` is too large or comprehensive to execute cleanly in a single pass.
2. The `PLAN.md` spans two or more distinct project scopes (e.g., frontend, backend, infra, or multiple packages), task types (`feature`, `bugfix`, `refactor`, `docs`), or independent deliverables that benefit from separate tracking.
3. Work can be split into independent units executed **in parallel or asynchronously** by different agents or contributors.

- **Template**: Start from [TASKS.md](./resources/TASKS.md).
- **Rules**:
  - Skip `TASKS.md` for small, linear changes where `PLAN.md` is sufficient.
  - Every task must specify an ID, status (`pending`, `in_progress`, `blocked`, `done`), dependencies, owned files/scope (to avoid merge conflicts between parallel agents), and clear done criteria.
  - Keep task statuses updated as work progresses.

### 4. `workflow/<change-slug>/REVIEW.md` — Record Code Review Findings

Create or update `workflow/<change-slug>/REVIEW.md` when a human or reviewing agent inspects the implemented changes.

- **Purpose**: Centralize actionable review observations that require attention before merging.
- **Template**: Start from [REVIEW.md](./resources/REVIEW.md).
- **Rules**:
  - Categorize every observation by severity (`blocker`, `major`, `minor`, `question`).
  - Point to exact file paths, line ranges, or symbols, and explain *why* the issue matters.
  - Track resolution status (`open`, `resolved`, `wontfix`) so nothing is lost across review rounds.

### 5. `workflow/<change-slug>/VALIDATION.md` — Product Delivery Validation (Conditional)

Create `workflow/<change-slug>/VALIDATION.md` after `REVIEW.md` is approved whenever `workflow/BACKLOG.md` was used or a Product Owner / stakeholder needs a structured handoff to validate the delivered value.

- **Purpose**: Translate the completed engineering delivery back into plain-language product outcomes, validation steps, and formal acceptance sign-off.
- **Template**: Start from [VALIDATION.md](./resources/VALIDATION.md).
- **Rules**:
  - Prepared by the Architect (or lead engineer) with a **Delivery Summary**, **Backlog Value Mapping**, and observable **How to Validate** scenarios.
  - Reviewed and signed off by the Product Owner (`Approved` or `Changes Requested`) before closing the cycle.

## Verification & Cleanup

Before considering a change (`workflow/<change-slug>/`) complete:

1. Every acceptance criterion in `workflow/<change-slug>/SPEC.md` is satisfied and verified by tests or explicit checks.
2. All steps in `workflow/<change-slug>/PLAN.md` (and `workflow/<change-slug>/TASKS.md`, if used) are completed or explicitly deferred.
3. All `blocker` and `major` items in `workflow/<change-slug>/REVIEW.md` are marked `resolved` (or `wontfix` with rationale).
4. If `workflow/<change-slug>/VALIDATION.md` is used, the Product Owner has marked it `Approved` (and updated the corresponding item status in `workflow/BACKLOG.md` to `Done`).
5. **Ask the user for final sign-off and cleanup**: Once the plan, tasks (if any), review, and validation (if used) are complete, explicitly ask the user if everything is ready and whether the completed `workflow/<change-slug>/` artifacts can be cleaned up.
   - **Never delete** the root `workflow/` directory or `workflow/BACKLOG.md`.
   - When the user confirms cleanup for a completed change, **delete the completed `workflow/<change-slug>/` subdirectory** (along with its `SPEC.md`, `PLAN.md`, `TASKS.md`, `REVIEW.md`, `VALIDATION.md`, and `assets/`), without touching other active `workflow/<other-slug>/` subdirectories.
   - If `workflow/BACKLOG.md` was used, preserve the backlog table (keeping ongoing items), clear only the completed demand brief if appropriate, and remove completed-change files inside `workflow/assets/` that are no longer referenced by ongoing `BACKLOG.md` items.
