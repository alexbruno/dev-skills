---
name: verification-and-testing
description: >-
  Enforce evidence-based verification and meaningful test coverage before
  claiming a task complete. Use when writing tests, validating `SPEC.md`
  acceptance criteria, or verifying builds/linters/runtime after changes. Do
  not use for implementation without verification scope.
---

# Verification & Testing

Never claim a change works, passes, or is complete without **fresh, direct verification evidence** collected after the final edit.

## Core Rule

**Evidence before completion claims.** If you did not run a check after your last change and observe its output, do not report it as passing.

## 1. Align Verification with `workflow/<change-slug>/SPEC.md`

- Map every acceptance criterion in `workflow/<change-slug>/SPEC.md` and the verification strategy in `workflow/<change-slug>/PLAN.md` to a concrete check (automated test, build, lint, typecheck, or explicit runtime check).
- Treat untested acceptance criteria as incomplete work.

## 2. Write Meaningful Tests

- **Test behavior, not implementation details**: Assert observable outputs, state transitions, and side effects so tests survive safe refactoring (`code-simplification`).
- **Cover the edges**: Include boundary values, empty/null inputs, invalid states, and error paths—not just the happy path.
- **Keep tests isolated and deterministic**: Avoid shared mutable state, real network calls, or time-dependent flakiness unless explicitly testing integration boundaries.
- **Never weaken assertions**: Do not relax, skip, or mock away the core behavior under test just to make a suite green.

## 3. Execution Gate After Final Edit

> **Anti-hallucination gate**: If no verification command was executed after the final edit, the status is **`UNVERIFIED`**, not `done`. Do not claim completion without fresh evidence.

Before handing off work or moving to `workflow/<change-slug>/REVIEW.md`:

1. Run the relevant unit/integration tests, linter, typechecker, and build commands.
2. Confirm **all** executed checks pass with zero skipped or relaxed assertions.
3. Report the exact scope of what was run **and include the Verification Evidence block (Command / Observed / Not verified) for each check** (see Section 4).

## 4. Verification Evidence Format

Every verification claim must include a structured evidence block with three fields:

| Field | Description |
|-------|-------------|
| **Command** | The exact command run (including flags, paths, environment). |
| **Observed** | The raw result — stdout/stderr snippet, exit code, pass/fail count, coverage number. |
| **Not verified** | What was not checked (scope limits, environment gaps, manual-only steps). |

**Example**:
```
Command: npm test -- --reporter=verbose
Observed: 42 passed, 0 failed; exit code 0; coverage 87%
Not verified: E2E tests not run (requires running backend); coverage only for unit tests.
```
