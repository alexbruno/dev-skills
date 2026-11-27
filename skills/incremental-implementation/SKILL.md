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

**Foundation**: Apply the KISS-first doctrine in `code-simplification`: KISS is the master philosophy, SOLID is a contextual heuristic for object-oriented design, and KISS/YAGNI win ties. Where SOLID applies, grade a deviation by concrete harm (testability, coupling, change amplification).

## Core Rule

**One slice, one verification cycle.** Do not start the next slice while the current slice has failing tests, type errors, or a broken build.

**Doctrine check**: Each slice must pass the `code-simplification` doctrine — KISS first; where SOLID applies, grade any deviation by concrete harm.

## When to Apply

- Executing steps from `workflow/<change-slug>/PLAN.md` or tasks from `workflow/<change-slug>/TASKS.md`.
- Any change touching two or more files or exceeding ~100 lines of code.
- Refactoring existing modules where behavior must remain intact at every step.

## The Increment Loop

For each step in `workflow/<change-slug>/PLAN.md` (or task in `workflow/<change-slug>/TASKS.md`):

1. **Scope the Slice**: Pick the smallest cohesive unit that delivers testable progress toward `workflow/<change-slug>/SPEC.md`. **Prefer vertical slices that are KISS-compliant (minimal, direct) per `code-simplification`.**
2. **Implement Minimally**: Write only the code required for this slice—**avoid speculative scaffolding for future slices (YAGNI/KISS)**. If an abstraction isn't needed *now*, don't build it.
3. **Test & Verify Immediately**: Run targeted unit/integration tests, typecheck, and linter to confirm the slice works and introduces no regressions.
4. **Doctrine Self-Review**: Before checkpoint, ask:
   - "Is this the simplest thing that works for this slice?"
   - "Where SOLID applies, does this slice deviate, and is there concrete harm?"
5. **Checkpoint**: Update status in `workflow/<change-slug>/PLAN.md` or `workflow/<change-slug>/TASKS.md` (e.g., mark `done`), and create a clean commit/checkpoint when appropriate so rollback is effortless.
6. **Repeat**: Advance to the next slice only after the current one is green **and** doctrine-compliant.

## Slicing Strategies (Doctrine-Aligned)

Choose the slicing strategy that best fits the work — **always preferring the strategy that yields the simplest, most doctrine-compliant slices**:

- **Vertical Slices (Default)**: Implement one thin end-to-end capability across layers (e.g., schema → service → endpoint → test) rather than building all backend files first and all frontend/test files last. **Each vertical slice should be a complete, testable feature with a single responsibility.**
- **Risk-First Slicing**: Tackle the highest-uncertainty assumption, integration, or performance bottleneck in the earliest slice so surprises surface immediately. **Keep the risk-mitigation slice itself simple (KISS) — don't over-engineer the probe.**
- **Contract-First Slicing**: Define types, interfaces, and test assertions first, verify they compile/fail as expected, then implement the concrete behavior.

## Doctrine Guardrails for Slices

- **No speculative abstractions**: Do not create interfaces, factories, strategies, or base classes "for future slices." Add them only when a *current* slice demonstrably needs them (YAGNI).
- **No slice stacking**: Fix failing checks (tests, lint, typecheck) in the current slice before moving on. A broken slice is not a foundation.
- **Keep diffs reviewable**: If a slice grows too large (> ~200 lines or > 3 files), split it into smaller sub-slices. Large slices hide complexity.
- **Stay aligned with `SPEC.md`**: Do not add unrequested features while implementing a slice.
- **Single responsibility per slice**: Each slice should ideally map to one logical responsibility. If a slice touches unrelated concerns, split it.
- **Depend on abstractions within slices**: Even in a slice, prefer injecting interfaces over instantiating concretions (DIP). If the abstraction doesn't exist yet, create the minimal interface *for this slice only*.

## Anti-Patterns to Avoid

| Anti-Pattern | Doctrine Violation | Remediation |
|--------------|---------------------|-------------|
| **Horizontal slicing** (all models, then all services, then all controllers) | Violates vertical slice principle; delays integration testing; encourages anemic models | Use vertical slices by default |
| **Building infrastructure "for later"** | YAGNI violation (KISS); speculative generality | Build infrastructure only when a slice consumes it |
| **Fat slices doing multiple things** | Hard to verify; hides coupling | Split into atomic, single-responsibility slices |
| **Concrete dependencies in slices** | Hard to test in isolation | Define and inject minimal interfaces |
| **Skipping verification to "go faster"** | Defeats the purpose; compounds risk | Verification is the speed enabler, not the cost |

## Verification Checklist per Slice

Before marking a slice `done`:

- [ ] All tests pass (unit + integration for the slice)
- [ ] Typecheck passes
- [ ] Linter passes
- [ ] Doctrine check: KISS first; where SOLID applies, grade any deviation by concrete harm per `code-simplification`
- [ ] No unrequested scope creep
- [ ] Status updated in `PLAN.md`/`TASKS.md`