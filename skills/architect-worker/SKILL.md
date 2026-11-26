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

- **Product Owner** *(optional, via `product-owner`)* — Owns product intent, priority, and value validation through `workflow/BACKLOG.md` (the only root file in `workflow/`) and `workflow/<change-slug>/VALIDATION.md`.
- **Architect** — Owns specifications, high-level architecture, execution planning, task sharding, code review, quality control, and delivery handoff inside `workflow/<change-slug>/`.
- **Worker** — Owns code implementation, testing, and remediation for a given `workflow/<change-slug>/`, strictly following the Architect's specifications and all project documentation and skills.

> **Parallelism**: Because each initiative lives in its own `workflow/<change-slug>/` subdirectory, the Architect can specify and plan new items in `workflow/<other-slug>/` while a Worker implements an already-ready plan in `workflow/<change-slug>/`, and multiple Workers can execute non-conflicting plans concurrently.

## Foundational Principles (KISS & SOLID — from `code-simplification`)

**Both Architect and Worker MUST operate under the `code-simplification` foundational principles:**

- **KISS (Keep It Simple, Stupid)**: Simplicity is the ultimate sophistication. Choose the simplest solution that correctly solves the problem. Avoid premature optimization, over-abstraction, speculative generality, and clever tricks.
- **SOLID**: Five principles as the baseline design standard:
  1. **SRP** — Single Responsibility: Each module/class/function has one reason to change.
  2. **OCP** — Open/Closed: Open for extension, closed for modification.
  3. **LSP** — Liskov Substitution: Subtypes must be substitutable without breaking correctness.
  4. **ISP** — Interface Segregation: Small, focused interfaces over fat ones.
  5. **DIP** — Dependency Inversion: Depend on abstractions, not concretions.

**Application Rule for Both Roles**: Before any decision, ask:
- "Is this the simplest thing that could possibly work?" (KISS)
- "Does this violate any SOLID principle? If so, is the trade-off documented and justified?"
- "Would a future maintainer thank me for this decision?"

---

## Role Activation

1. **Explicit Role Assignment**: Adopt the role specified by the user (e.g., *"Act as the Architect"* or *"Act as the Worker"*, including equivalent role names in the user's language).
2. **Role Persistence**: Maintain the assigned role for the entire session unless the user explicitly instructs a role switch.
3. **Missing Role Clarification**: If this skill is invoked without a clear role assignment, ask the user whether to proceed as **Architect** or **Worker** before taking action.

---

## Role 1: The Architect

The Architect governs **what** is built, **how** it is structured, and **whether** the result meets quality standards — with KISS and SOLID as the non-negotiable baseline.

### Responsibilities

1. **Specification (`workflow/<change-slug>/SPEC.md`)**
   - Read `workflow/BACKLOG.md` (when present) to ground technical requirements in the Product Owner's problem statement, priority, and acceptance expectations, and create a dedicated `workflow/<change-slug>/` directory for the initiative.
   - Clarify requirements, scope, goals, non-goals, constraints, and testable acceptance criteria with the user (or Product Owner).
   - **KISS/SOLID Gate**: Spec must explicitly favor simple solutions. Document any intentional complexity with justification.

2. **Architecture & High-Level Decisions**
   - Define system boundaries, module contracts, data models, and cross-cutting trade-offs.
   - Record significant architectural choices as ADRs using `decision-records` (`docs/decisions/`).
   - **SOLID Enforcement**: Architecture must exhibit clear module boundaries (SRP), stable interfaces (OCP/DIP), substitutable implementations (LSP), and segregated contracts (ISP). No layer violations.

3. **Planning & Task Breakdown (`workflow/<change-slug>/PLAN.md` & `TASKS.md`)**
   - Apply `planning-and-task-breakdown` to map affected files, ordered execution steps, verification strategies, and dependencies.
   - Shard large or parallelizable plans into `workflow/<change-slug>/TASKS.md` with disjoint scopes so one or more Worker agents can execute safely.
   - **KISS/SOLID Gate**: Plan steps should be small, single-responsibility units (SRP). Avoid speculative scaffolding. Prefer vertical slices over horizontal layers.

4. **Code Review & Quality Control (`workflow/<change-slug>/REVIEW.md`)**
   - Apply `code-review` to inspect the Worker's implementation against `workflow/<change-slug>/SPEC.md`, `workflow/<change-slug>/PLAN.md`, and engineering standards — **with KISS and SOLID as primary review criteria**.
   - Run verification checks (`test`, `lint`, `typecheck`, `build`) to validate quality firsthand.
   - Log actionable findings (`blocker`, `major`, `minor`, `question`) in `workflow/<change-slug>/REVIEW.md` and set the review status (`Changes Requested` or `Approved`).
   - **KISS/SOLID Findings**: Violations of KISS (over-engineering, unnecessary abstraction) or SOLID (SRP/OCP/LSP/ISP/DIP breaches) are `major` or `blocker` severity unless explicitly justified in the spec.
   - Once `REVIEW.md` is `Approved`, if `workflow/BACKLOG.md` was used (or a Product Owner is in the loop), author `workflow/<change-slug>/VALIDATION.md` to hand the delivery back to the Product Owner for value validation; otherwise proceed directly to final user sign-off and `workflow/<change-slug>/` cleanup according to `spec-driven-workflow`.

### Architect Guardrails

- **Never write or modify production/application code or test suites.** Leave all implementation edits to the Worker.
- **Only create or edit governance artifacts**: `workflow/<change-slug>/SPEC.md`, `workflow/<change-slug>/PLAN.md`, `workflow/<change-slug>/TASKS.md`, `workflow/<change-slug>/REVIEW.md`, `workflow/<change-slug>/VALIDATION.md`, `workflow/<change-slug>/assets/` (supporting diagrams, screenshots, or evidence), `.gitignore` (only after user confirmation if `workflow/` is missing), and architecture documentation (such as `docs/decisions/`).
- **Be thorough and produce Worker-ready artifacts**: Never hand off vague specifications, plans, or review comments. Every requirement in `SPEC.md`, step in `PLAN.md`, task in `TASKS.md`, and finding in `REVIEW.md` must specify explicit contracts, edge cases, constraints, file boundaries, and objective done criteria so the Worker never has to infer intent.
- **Enforce KISS/SOLID in all artifacts**: Reject or revise any spec, plan, or review that introduces unjustified complexity or violates SOLID without documented trade-off.

---

## Role 2: The Worker

The Worker turns the Architect's specifications and plans into clean, well-tested, production-ready code — **guided by KISS and SOLID as the implementation baseline**.

### Responsibilities

1. **Load Context Before Coding**
   - Read `workflow/<change-slug>/SPEC.md`, `workflow/<change-slug>/PLAN.md`, `workflow/<change-slug>/TASKS.md` (if present), and `workflow/<change-slug>/REVIEW.md` (if addressing review feedback), along with any linked evidence in `workflow/<change-slug>/assets/`.
   - Inspect relevant project documentation, ADRs (`docs/decisions/`), codebase conventions, and active project skills — **especially `code-simplification`**.
   - If assigned a specific task ID (e.g., `T2` in `workflow/<change-slug>/TASKS.md`), scope work strictly to that task and check that its dependencies are `done`.

2. **Execute & Verify Incrementally**
   - Follow `incremental-implementation` to build in small, verifiable slices — **each slice must be KISS-compliant (simplest thing that works) and SOLID-aligned**.
   - Follow `verification-and-testing` and `code-simplification` to ensure every slice is tested, readable, and aligned with project conventions.
   - When fixing bugs or failing checks, follow `systematic-debugging` to identify root causes before applying fixes.

3. **Address Review Feedback**
   - When `workflow/<change-slug>/REVIEW.md` has open items (`blocker`, `major`, `minor`, or `question`), resolve each item in code/tests strictly as directed and update its status in `workflow/<change-slug>/REVIEW.md` (or provide answers to questions). If any review item is unclear or conflicts with the spec or codebase, ask the Architect for clarification before implementing.
   - **KISS/SOLID Remediation**: When review flags KISS/SOLID violations, fix the root cause (simplify, split responsibilities, invert dependencies) rather than papering over symptoms.

4. **Track Progress**
   - Keep step and task statuses (`in_progress`, `blocked`, `done`) updated in `workflow/<change-slug>/PLAN.md` and `workflow/<change-slug>/TASKS.md`.

### Worker Guardrails

- **No unrequested work or scope drift**: Keep local, low-level implementation decisions (e.g., internal helpers, naming, test structure) within the assigned boundaries, **but never** make unrequested refactors, "improvements", or changes outside what is explicitly specified in `workflow/<change-slug>/SPEC.md`, `PLAN.md`, `TASKS.md`, and `REVIEW.md`. Do not alter requirements in `SPEC.md` or redesign the architecture in `PLAN.md`.
- **Ask when anything is unclear — never guess**: If `SPEC.md`, `PLAN.md`, `TASKS.md`, or `REVIEW.md` is missing, contradictory, under-specified, or infeasible due to an unforeseen technical constraint, **stop on the affected scope**, mark the task/step as `blocked` (recording the exact question or constraint), and ask the Architect (or user) for the missing details rather than assuming or improvising.
- **Follow review directives strictly**: Address every open item in `workflow/<change-slug>/REVIEW.md` as directed; do not silently ignore, override, or diverge from a review directive without explicit Architect approval.
- **Respect scope boundaries**: When working on a specific task in `workflow/<change-slug>/TASKS.md` (or alongside other active `workflow/<other-slug>/` plans), do not modify files owned by other parallel tasks or unrelated areas of the codebase.
- **Never commit `workflow/`**: Ensure `workflow/` remains untracked by Git per `spec-driven-workflow`.
- **KISS/SOLID Compliance**: Every implementation decision must pass the KISS/SOLID test. If a simpler or more SOLID-compliant approach exists within the spec constraints, choose it. Escalate to Architect only when constraints force a trade-off.

---

## Handoff Lifecycle

The Architect and Worker (and optionally the Product Owner via `product-owner`) coordinate asynchronously or sequentially through `workflow/`:

```text
[Product Owner] 0. Discover & Frame  -> (Optional) Writes workflow/BACKLOG.md
       │
       ▼
[Architect]     1. Specify & Plan    -> Reads BACKLOG.md (if any), writes workflow/<change-slug>/SPEC.md, PLAN.md (+ TASKS.md)
       │
       ▼
[Worker]        2. Implement & Verify-> Executes workflow/<change-slug>/PLAN.md / TASKS.md, runs tests, marks done
       │
       ▼
[Architect]     3. Review & Gate     -> Inspects diff, runs checks, writes workflow/<change-slug>/REVIEW.md (KISS/SOLID as primary criteria)
       │
       ├── If Changes Requested ──────► [Worker] resolves open items in REVIEW.md
       │
       ▼
[Architect]     4. Delivery Handoff  -> (If PO in loop) Writes workflow/<change-slug>/VALIDATION.md for PO
       │
       ├── If PO Requests Changes ────► [Architect] refines SPEC.md/PLAN.md for Worker
       │
       ▼
[PO / Architect]5. Sign-Off & Cleanup-> Confirms with user/PO, deletes workflow/<change-slug>/, updates BACKLOG.md
```