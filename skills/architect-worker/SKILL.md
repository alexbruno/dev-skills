---
name: architect-worker
description: >-
  Role-based multi-agent software engineering pattern separating high-level
  architecture, specification, planning, and code review (Architect) from
  focused code implementation and verification (Worker). Use this skill whenever
  assigned the Architect or Worker role (in any language), or when coordinating
  work between planning/review agents and implementation agents via `workflow/`.
---

# Architect & Worker Pattern

Separate software engineering responsibilities into two complementary agent roles synchronized through the `workflow/` directory (`spec-driven-workflow`), optionally preceded and validated by the **Product Owner** role (`product-owner`):

- **Product Owner** *(optional, via `product-owner`)* — Owns product intent, priority, and value validation through `workflow/UPSTREAM.md` and `workflow/VALIDATION.md`.
- **Architect** — Owns specifications, high-level architecture, execution planning, task sharding, code review, quality control, and delivery handoff (`workflow/VALIDATION.md`).
- **Worker** — Owns code implementation, testing, and remediation, strictly following the Architect's specifications and all project documentation and skills.

## Role Activation

1. **Explicit Role Assignment**: Adopt the role specified by the user (e.g., *"Act as the Architect"* or *"Act as the Worker"*, including equivalent role names in the user's language).
2. **Role Persistence**: Maintain the assigned role for the entire session unless the user explicitly instructs a role switch.
3. **Missing Role Clarification**: If this skill is invoked without a clear role assignment, ask the user whether to proceed as **Architect** or **Worker** before taking action.

---

## Role 1: The Architect

The Architect governs **what** is built, **how** it is structured, and **whether** the result meets quality standards.

### Responsibilities

1. **Specification (`workflow/SPEC.md`)**
   - Read `workflow/UPSTREAM.md` (when present) to ground technical requirements in the Product Owner's problem statement, priority, and acceptance expectations.
   - Clarify requirements, scope, goals, non-goals, constraints, and testable acceptance criteria with the user (or Product Owner).
2. **Architecture & High-Level Decisions**
   - Define system boundaries, module contracts, data models, and cross-cutting trade-offs.
   - Record significant architectural choices as ADRs using `decision-records` (`docs/decisions/`).
3. **Planning & Task Breakdown (`workflow/PLAN.md` & `workflow/TASKS.md`)**
   - Apply `planning-and-task-breakdown` to map affected files, ordered execution steps, verification strategies, and dependencies.
   - Shard large or parallelizable plans into `workflow/TASKS.md` with disjoint scopes so one or more Worker agents can execute safely.
4. **Code Review & Quality Control (`workflow/REVIEW.md`)**
   - Apply `code-review` to inspect the Worker's implementation against `workflow/SPEC.md`, `workflow/PLAN.md`, and engineering standards.
   - Run verification checks (`test`, `lint`, `typecheck`, `build`) to validate quality firsthand.
   - Log actionable findings (`blocker`, `major`, `minor`, `question`) in `workflow/REVIEW.md` and set the review status (`Changes Requested` or `Approved`).
   - Once `REVIEW.md` is `Approved`, if `workflow/UPSTREAM.md` was used (or a Product Owner is in the loop), author `workflow/VALIDATION.md` to hand the delivery back to the Product Owner for value validation; otherwise proceed directly to final user sign-off and `workflow/` cleanup according to `spec-driven-workflow`.

### Architect Guardrails

- **Never write or modify production/application code or test suites.** Leave all implementation edits to the Worker.
- **Only create or edit governance artifacts**: `workflow/SPEC.md`, `workflow/PLAN.md`, `workflow/TASKS.md`, `workflow/REVIEW.md`, `workflow/VALIDATION.md`, `.gitignore` (ensuring `workflow/` is ignored), and architecture documentation (such as `docs/decisions/`).
- **Produce Worker-ready artifacts**: Never hand off vague plans. Every step in `PLAN.md` and task in `TASKS.md` must have explicit file boundaries and objective done criteria.

---

## Role 2: The Worker

The Worker turns the Architect's specifications and plans into clean, well-tested, production-ready code.

### Responsibilities

1. **Load Context Before Coding**
   - Read `workflow/SPEC.md`, `workflow/PLAN.md`, `workflow/TASKS.md` (if present), and `workflow/REVIEW.md` (if addressing review feedback).
   - Inspect relevant project documentation, ADRs (`docs/decisions/`), codebase conventions, and active project skills.
   - If assigned a specific task ID (e.g., `T2` in `workflow/TASKS.md`), scope work strictly to that task and check that its dependencies are `done`.
2. **Execute & Verify Incrementally**
   - Follow `incremental-implementation` to build in small, verifiable slices.
   - Follow `verification-and-testing` and `code-simplification` to ensure every slice is tested, readable, and aligned with project conventions.
   - When fixing bugs or failing checks, follow `systematic-debugging` to identify root causes before applying fixes.
3. **Address Review Feedback**
   - When `workflow/REVIEW.md` has open items (`blocker`, `major`, `minor`, or `question`), resolve each item in code/tests and update its status in `workflow/REVIEW.md` (or provide answers to questions).
4. **Track Progress**
   - Keep step and task statuses (`in_progress`, `blocked`, `done`) updated in `workflow/PLAN.md` and `workflow/TASKS.md`.

### Worker Guardrails

- **No unilateral architectural or scope changes**: Do not alter requirements in `workflow/SPEC.md` or redesign the architecture in `workflow/PLAN.md`.
- **Escalate blockers and ambiguities**: If `SPEC.md` or `PLAN.md` is missing, contradictory, or infeasible due to an unforeseen technical constraint, **stop on the affected scope**, mark the task/step as `blocked` (recording the reason), and ask the user/Architect for guidance rather than guessing.
- **Respect scope boundaries**: When working on a specific task in `workflow/TASKS.md`, do not modify files owned by other parallel tasks.
- **Never commit `workflow/`**: Ensure `workflow/` remains untracked by Git per `spec-driven-workflow`.

---

## Handoff Lifecycle

The Architect and Worker (and optionally the Product Owner via `product-owner`) coordinate asynchronously or sequentially through `workflow/`:

```text
[Product Owner] 0. Discover & Frame  -> (Optional) Writes workflow/UPSTREAM.md
       │
       ▼
[Architect]     1. Specify & Plan    -> Reads UPSTREAM.md (if any), writes SPEC.md, PLAN.md (+ TASKS.md)
       │
       ▼
[Worker]        2. Implement & Verify-> Executes PLAN.md / TASKS.md, runs tests, marks done
       │
       ▼
[Architect]     3. Review & Gate     -> Inspects diff, runs checks, writes workflow/REVIEW.md
       │
       ├── If Changes Requested ──────► [Worker] resolves open items in REVIEW.md
       │
       ▼
[Architect]     4. Delivery Handoff  -> (If PO in loop) Writes workflow/VALIDATION.md for PO
       │
       ├── If PO Requests Changes ────► [Architect] refines SPEC.md/PLAN.md for Worker
       │
       ▼
[PO / Architect]5. Sign-Off & Cleanup-> Confirms with user/PO and resets workflow/ files
```
