---
name: code-review
description: >-
  Perform structured, multi-dimensional code reviews aligned with the
  `spec-driven-workflow` (`workflow/<change-slug>/SPEC.md`, `PLAN.md`,
  `REVIEW.md`). Use this skill after implementing a feature or bug fix,
  before merging a PR, or when reviewing changes written by another agent or
  human.
---

# Code Review

Review code changes against both **engineering quality** and **specification intent**, recording every actionable finding in `workflow/<change-slug>/REVIEW.md`.

**Foundation**: This review skill operates under the **`code-simplification` foundational principles** — **KISS (Keep It Simple, Stupid)** and **SOLID** are the non-negotiable baseline for all review judgments. Every finding should be evaluated through this lens.

## Review Philosophy

- **Net improvement over perfection**: Approve changes that clearly improve overall code health and satisfy `workflow/<change-slug>/SPEC.md`, even if minor stylistic preferences differ.
- **Evidence over opinion**: Ground every finding in a concrete file path, line range or symbol, and explain *why* it matters — **citing KISS/SOLID principle violations explicitly when applicable**.
- **Zero silent passes**: Do not approve or finish work while any `blocker` or `major` finding remains `open`.
- **KISS/SOLID as Primary Criteria**: Simplicity (KISS) and SOLID compliance are first-class review dimensions, not afterthoughts. Over-engineering, unnecessary abstraction, SRP violations, DIP breaches, etc., are `major` or `blocker` severity.

## 1. Load Workflow Context First

Before inspecting the diff, read the active `workflow/<change-slug>/` artifacts:

1. **`workflow/<change-slug>/SPEC.md`** — Verify goals, non-goals, constraints, and acceptance criteria.
2. **`workflow/<change-slug>/PLAN.md`** (and **`workflow/<change-slug>/TASKS.md`**, if present) — Understand the intended technical approach and module scope.
3. **`workflow/<change-slug>/REVIEW.md`** — Check prior review rounds so resolved items are not re-reported and open items are re-verified.

If `workflow/<change-slug>/REVIEW.md` does not exist yet, create it (using the `spec-driven-workflow` `REVIEW.md` template if installed).

## 2. Six-Axis Review Framework

Evaluate the diff (e.g., `git diff`, staged changes, or target PR) across all **six** dimensions — **KISS & SOLID added as explicit axes**:

### Axis 1: KISS — Simplicity & Directness (NEW — Primary)
- Is this the **simplest thing that could possibly work**?
- Are there unnecessary abstractions, indirection layers, speculative factories, or premature optimizations?
- Can a junior developer understand the code in ~30 seconds?
- Does every abstraction justify itself with a clear, measurable benefit?
- **Violations**: Over-engineering, clever one-liners, YAGNI code, speculative generality → `major`/`blocker`

### Axis 2: SOLID — Design Principles Compliance (NEW — Primary)
- **SRP**: Does each class/module/function have a single, clear responsibility? One reason to change?
- **OCP**: Can behavior be extended without modifying existing code? Are extension points used instead of modification?
- **LSP**: Are subtypes safely substitutable for their base types? Do inheritance hierarchies respect behavioral contracts?
- **ISP**: Are interfaces small and focused? Are clients forced to depend on methods they don't use?
- **DIP**: Does code depend on abstractions (interfaces/protocols) rather than concretions? Are high-level modules decoupled from low-level details?
- **Violations**: Each SOLID breach is `major` unless the spec explicitly requires the trade-off with documented justification.

### Axis 3: Correctness & Spec Alignment
- Does the implementation satisfy every acceptance criterion in `workflow/<change-slug>/SPEC.md`?
- Does it stay within scope (no unintended changes outside `SPEC.md` / `PLAN.md`)?
- Are edge cases (null/empty values, boundaries, concurrency) and error paths handled?
- Do tests verify the new behavior and guard against regressions?

### Axis 4: Readability & Clarity
- Are names clear, consistent with the codebase, and self-explanatory?
- Is control flow shallow and easy to follow without dead code or over-engineering?
- **KISS overlap**: Cryptic one-liners, nested ternaries, dense chains → violations here AND Axis 1.

### Axis 5: Architecture & Maintainability
- Does the change follow existing codebase patterns and respect module boundaries?
- Does it avoid duplicated logic or unnecessary coupling?
- **SOLID overlap**: Layer violations, circular dependencies, missing abstractions → violations here AND Axis 2.

### Axis 6: Security
- Is untrusted input validated and sanitized at system boundaries?
- Are secrets, tokens, and sensitive data kept out of code, configs, and logs?
- Are queries parameterized and auth/authorization checks enforced?

### Axis 7: Performance
- Are there obvious N+1 queries, unbounded loops/fetches, memory leaks, or blocking I/O on hot paths?

> **Axis Priority**: Axes 1 & 2 (KISS & SOLID) are **gatekeeping dimensions** — violations here often indicate systemic design issues that compound over time. Axes 3–7 are compliance/quality dimensions.

## 3. Record Findings in `workflow/<change-slug>/REVIEW.md`

Log every finding in `workflow/<change-slug>/REVIEW.md` using consistent severity tiers and resolution statuses:

- **Severities**:
  - `blocker`: Must be fixed before completion (broken spec requirement, security flaw, data-loss risk, failing build/tests, **KISS/SOLID violation with no documented justification**).
  - `major`: Must be addressed before completion (edge-case bug, weak error handling, architectural violation, missing test coverage, **KISS/SOLID violation with weak justification**).
  - `minor`: Optional improvement or nit (naming, minor readability cleanup); never blocks approval.
  - `question`: Clarification needed from the author or implementing agent.
- **Statuses**: `open` | `resolved` | `wontfix` (with brief rationale).

Each finding entry must include:
- A unique ID (e.g., `[R1]`, `[Q1]`), exact file path and line/symbol reference, severity, status, **Issue**, and **Action**.
- **For KISS/SOLID findings**: Explicitly cite the principle (e.g., "SRP violation: `UserService` handles auth, persistence, and notifications — split into `AuthService`, `UserRepository`, `NotificationService`") and the recommended remediation.

## 4. Verification & Sign-Off

1. Run the project's verification commands (`test`, `lint`, `typecheck`, `build` as applicable) rather than relying on inspection alone.
2. Update `workflow/<change-slug>/REVIEW.md` status (`Open` | `Changes Requested` | `Approved`):
   - **Approved**: All `SPEC.md` acceptance criteria are met, checks pass, and zero `blocker` or `major` items remain `open` — **including KISS/SOLID axes**.
   - **Changes Requested**: One or more `blocker` or `major` items remain `open`.
3. Follow the `spec-driven-workflow` cleanup rule: keep `workflow/<change-slug>/REVIEW.md` until validation/sign-off is complete, and only delete the completed `workflow/<change-slug>/` directory after the user confirms cleanup.

---

## Quick Reference: KISS/SOLID Finding Templates

### KISS Violation
```markdown
#### [R3] — [KISS] — src/services/OrderProcessor.ts:45-78
- **Severity**: major
- **Issue**: `OrderProcessor` uses a `StrategyFactory` + `CommandBuilder` + `ExecutorChain` for a simple linear workflow (validate → calculate → persist). Three abstraction layers for ~30 lines of straightforward logic.
- **Action**: Inline the workflow into a single `processOrder(order)` function with guard clauses. Remove `StrategyFactory`, `CommandBuilder`, `ExecutorChain`. If extension is needed later, add it then (YAGNI).
```

### SOLID Violation (SRP)
```markdown
#### [R7] — [SOLID:SRP] — src/modules/UserService.ts:1-120
- **Severity**: blocker
- **Issue**: `UserService` handles authentication (login, token refresh), persistence (CRUD), and notifications (email, push). Three distinct reasons to change.
- **Action**: Split into `AuthService` (auth), `UserRepository` (persistence), `NotificationService` (notifications). `UserService` becomes a thin coordinator or is removed. Update DI container registration.
```

### SOLID Violation (DIP)
```markdown
#### [R12] — [SOLID:DIP] — src/controllers/PaymentController.ts:22
- **Severity**: major
- **Issue**: `PaymentController` directly instantiates `StripePaymentGateway` (concrete). Violates DIP — cannot test with mock, cannot swap provider.
- **Action**: Define `PaymentGateway` interface. Inject `PaymentGateway` into `PaymentController` constructor. Register `StripePaymentGateway` as implementation in DI container.
```