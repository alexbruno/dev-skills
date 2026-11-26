---
name: incremental-implementation
description: >-
  Build features and refactorings in small, verified vertical slices instead of
  large unverified code dumps. Use this skill when executing
  `workflow/<change-slug>/PLAN.md` or `workflow/<change-slug>/TASKS.md`,
  modifying multiple files, or implementing changes expected to exceed ~100
  lines.
---

# Incremental Implementation

Deliver changes in **thin, verifiable increments**—one working slice at a time. Never write an entire multi-file feature in a single unverified pass.

**Foundation**: This skill operates under the **`code-simplification` foundational principles** — **KISS (Keep It Simple, Stupid)** and **SOLID**. Every slice must be the simplest thing that works (KISS) and must respect SOLID design principles. Incremental implementation is the *mechanism*; KISS/SOLID are the *quality standard* for each increment.

## Core Rule

**One slice, one verification cycle.** Do not start the next slice while the current slice has failing tests, type errors, or a broken build.

**KISS/SOLID Gate**: Each slice must pass the KISS/SOLID test:
- Is this the simplest implementation that satisfies the slice's acceptance criteria?
- Does this slice introduce any SOLID violations? If so, is there a documented justification or a follow-up task to remediate?

## When to Apply

- Executing steps from `workflow/<change-slug>/PLAN.md` or tasks from `workflow/<change-slug>/TASKS.md`.
- Any change touching two or more files or exceeding ~100 lines of code.
- Refactoring existing modules where behavior must remain intact at every step.

## The Increment Loop

For each step in `workflow/<change-slug>/PLAN.md` (or task in `workflow/<change-slug>/TASKS.md`):

1. **Scope the Slice**: Pick the smallest cohesive unit that delivers testable progress toward `workflow/<change-slug>/SPEC.md`. **Prefer vertical slices that are KISS-compliant (minimal, direct) and SOLID-aligned (single responsibility, depend on abstractions).**
2. **Implement Minimally**: Write only the code required for this slice—**avoid speculative scaffolding for future slices (YAGNI/KISS)**. If an abstraction isn't needed *now*, don't build it.
3. **Test & Verify Immediately**: Run targeted unit/integration tests, typecheck, and linter to confirm the slice works and introduces no regressions.
4. **KISS/SOLID Self-Review**: Before checkpoint, ask:
   - "Is this the simplest thing that works for this slice?"
   - "Does this slice have a single clear responsibility? (SRP)"
   - "Am I depending on abstractions, not concretions? (DIP)"
   - "Are interfaces small and focused? (ISP)"
5. **Checkpoint**: Update status in `workflow/<change-slug>/PLAN.md` or `workflow/<change-slug>/TASKS.md` (e.g., mark `done`), and create a clean commit/checkpoint when appropriate so rollback is effortless.
6. **Repeat**: Advance to the next slice only after the current one is green **and** KISS/SOLID-compliant.

## Slicing Strategies (KISS/SOLID-Aligned)

Choose the slicing strategy that best fits the work — **always preferring the strategy that yields the simplest, most SOLID-compliant slices**:

- **Vertical Slices (Default)**: Implement one thin end-to-end capability across layers (e.g., schema → service → endpoint → test) rather than building all backend files first and all frontend/test files last. **Each vertical slice should be a complete, testable feature with a single responsibility (SRP).**
- **Risk-First Slicing**: Tackle the highest-uncertainty assumption, integration, or performance bottleneck in the earliest slice so surprises surface immediately. **Keep the risk-mitigation slice itself simple (KISS) — don't over-engineer the probe.**
- **Contract-First Slicing**: Define types, interfaces, and test assertions first, verify they compile/fail as expected, then implement the concrete behavior. **This naturally enforces DIP (depend on abstractions) and ISP (small interfaces).**

## KISS/SOLID Guardrails for Slices

- **No speculative abstractions**: Do not create interfaces, factories, strategies, or base classes "for future slices." Add them only when a *current* slice demonstrably needs them (YAGNI).
- **No slice stacking**: Fix failing checks (tests, lint, typecheck) in the current slice before moving on. A broken slice is not a foundation.
- **Keep diffs reviewable**: If a slice grows too large (> ~200 lines or > 3 files), split it into smaller sub-slices. Large slices hide complexity and SOLID violations.
- **Stay aligned with `SPEC.md`**: Do not add unrequested features while implementing a slice.
- **Single responsibility per slice**: Each slice should ideally map to one logical responsibility. If a slice touches unrelated concerns, split it (SRP).
- **Depend on abstractions within slices**: Even in a slice, prefer injecting interfaces over instantiating concretions (DIP). If the abstraction doesn't exist yet, create the minimal interface *for this slice only*.

## Anti-Patterns to Avoid

| Anti-Pattern | KISS/SOLID Violation | Remediation |
|--------------|---------------------|-------------|
| **Horizontal slicing** (all models, then all services, then all controllers) | Violates vertical slice principle; delays integration testing; encourages anemic models | Use vertical slices by default |
| **Building infrastructure "for later"** | YAGNI violation (KISS); speculative generality | Build infrastructure only when a slice consumes it |
| **Fat slices doing multiple things** | SRP violation; hard to verify; hides coupling | Split into atomic, single-responsibility slices |
| **Concrete dependencies in slices** | DIP violation; hard to test in isolation | Define and inject minimal interfaces |
| **Skipping verification to "go faster"** | Defeats the purpose; compounds risk | Verification is the speed enabler, not the cost |

## Verification Checklist per Slice

Before marking a slice `done`:

- [ ] All tests pass (unit + integration for the slice)
- [ ] Typecheck passes
- [ ] Linter passes
- [ ] **KISS**: No unnecessary abstraction, indirection, or complexity
- [ ] **SRP**: Slice has one clear responsibility
- [ ] **DIP**: Dependencies are on abstractions (interfaces), not concretions
- [ ] **ISP**: Any new interfaces are small and focused
- [ ] **OCP/LSP**: No modifications to existing working code required for this slice (extension only); any subtyping is safe
- [ ] No unrequested scope creep
- [ ] Status updated in `PLAN.md`/`TASKS.md`