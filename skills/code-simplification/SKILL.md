---
name: code-simplification
description: >-
  Simplify code for human readability, clarity, and directness without
  changing behavior. Use this skill after implementing a feature, during
  refactoring, or when addressing code review feedback—especially when code feels
  over-engineered, deeply nested, or takes unnecessary detours to do simple
  things.
---

# Code Simplification

Make code **clean, direct, and easy for humans to read** while preserving exact runtime behavior. Prefer straightforward, objective implementations over clever tricks or premature abstractions.

## Foundational Principles (KISS & SOLID)

**KISS is the master philosophy.** YAGNI is the enforcement mechanism: no abstraction, layer, or pattern without a current need. **SOLID is a contextual heuristic for object-oriented design** — it applies to designs that use classes, interfaces, or explicit module contracts, and is **not** a mandate for procedural, functional, shell/script, or data/config code.

**Tie-breaker**: KISS/YAGNI win ties. When a SOLID principle and KISS/YAGNI conflict, the resulting deviation is acceptable without a documented trade-off.

### KISS — Keep It Simple, Stupid
- Simplicity is the ultimate sophistication. Choose the simplest solution that correctly solves the problem.
- Avoid premature optimization, over-abstraction, speculative generality, and clever tricks.
- If a junior developer can't understand the code in 30 seconds, it's too complex.
- Complexity must earn its keep—every abstraction must justify itself with clear, measurable benefit.

### SOLID — Contextual Heuristic for Object-Oriented Design
1. **SRP**: A class/module should have one reason to change. Each unit does one thing well.
2. **OCP**: Software entities should be open for extension but closed for modification.
3. **LSP**: Subtypes must be substitutable for their base types without altering correctness.
4. **ISP**: Clients should not be forced to depend on interfaces they don't use. Small, focused interfaces over fat ones.
5. **DIP**: Depend on abstractions, not concretions; high-level and low-level modules both depend on abstractions.

**Application Rule**: Before writing or modifying ANY code, ask:
- "Is this the simplest thing that could possibly work?" (KISS — unconditional)
- "Where SOLID applies, does this deviate, and is there concrete harm?" (SOLID — conditional)
- "Would a future maintainer thank me for this decision?"

---

## Core Principles

1. **Preserve Behavior Exactly**: Inputs, outputs, side effects, error semantics, and edge-case guards must remain identical. Existing tests must pass without modification.
2. **Clarity Over Cleverness (and Over Brevity)**: The goal is human comprehension, not fewer lines. Never replace readable code with cryptic one-liners, nested ternaries, or dense chains.
3. **Direct Over Indirection**: Do not take three hops to do something simple. Inline trivial single-use wrappers, pass-through helpers, and speculative abstractions that add ceremony without clarity.
4. **Chesterton's Fence**: Before deleting or rewriting code that looks redundant or defensive, understand *why* it was written (check callers, tests, comments, or `workflow/<change-slug>/SPEC.md`).

---
## When to Apply

- Right after a feature or bug fix works and tests pass, to clean up rough or over-complicated details.
- When functions have deep `if/else` nesting, long monolithic blocks, or unclear names.
- When `workflow/<change-slug>/REVIEW.md` flags readability, maintainability, or over-engineering issues.

---

## What to Simplify (with KISS & SOLID Lens)

- **Flatten control flow**: guard clauses and early returns instead of deep `if/else`. (SRP — where applicable)
- **Remove unnecessary indirection**: collapse pass-through functions, single-use factories, redundant layers. (KISS)
- **Make logic linear**: direct, step-by-step code instead of convoluted state flags. (KISS + SRP — where applicable)
- **Right-size functions**: split long functions only when each unit is a clear step, not to fragment trivial code. (SRP + ISP — where applicable)
- **Use clear names, trim noise**: self-explanatory identifiers; keep non-obvious *why* comments and existing docstrings, drop restatements. (KISS)
- **Unify genuine duplication**: one parameterized helper instead of parallel copies, only when it simplifies. (DRY, KISS first)
- **Enforce single responsibility**: split units with multiple reasons to change only when it reduces concrete harm such as change amplification. (SRP — where applicable)
- **Prefer composition over inheritance**: avoid deep hierarchies; use composition and delegation. (LSP + OCP — where applicable)
- **Keep interfaces small and focused**: don't force clients to implement unused methods. (ISP — where applicable)

---

## Guardrails (What NOT to Do)

- Do **not** alter public APIs, return shapes, or error handling unless explicitly requested.
- Do **not** refactor unrelated files outside the current change scope.
- Do **not** remove edge-case checks (`null`, empty, boundary conditions) unless proven unreachable.
- Do **not** add abstractions/patterns "for future flexibility" without a concrete, current need (YAGNI — KISS enforcement).
- Do **not** add ceremony to satisfy a principle. A SOLID deviation under KISS is acceptable without a documented trade-off.

---

## Verification

1. Review the diff to confirm every change is simpler, cleaner, and strictly behavior-preserving.
2. Run the test suite, linter, and typechecker to verify all checks pass unchanged.
3. **KISS/SOLID Check**: For each changed unit, verify:
   - Single, clear responsibility? (SRP, where applicable)
   - Open for extension without modification? (OCP, where applicable)
   - Subtypes safely substitutable? (LSP, where applicable)
   - Interfaces minimal and focused? (ISP, where applicable)
   - Depend on abstractions, not concretions? (DIP, where applicable)
   - Is this the simplest solution that works? (KISS — closing gate)
