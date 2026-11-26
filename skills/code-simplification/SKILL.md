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

**These principles MUST be the base of reasoning for ALL code development decisions.** They are not optional guidelines—they are the lens through which every design choice is evaluated.

### KISS — Keep It Simple, Stupid
- **Simplicity is the ultimate sophistication.** Choose the simplest solution that correctly solves the problem.
- Avoid premature optimization, over-abstraction, speculative generality, and clever tricks.
- If a junior developer can't understand the code in 30 seconds, it's too complex.
- Complexity must earn its keep—every abstraction, layer, or pattern must justify itself with clear, measurable benefit.

### SOLID — Five Principles of Object-Oriented Design
1. **Single Responsibility Principle (SRP)**: A class/module should have one, and only one, reason to change. Each unit does one thing well.
2. **Open/Closed Principle (OCP)**: Software entities should be open for extension but closed for modification. Extend behavior without changing existing code.
3. **Liskov Substitution Principle (LSP)**: Subtypes must be substitutable for their base types without altering correctness. Inheritance must respect behavioral contracts.
4. **Interface Segregation Principle (ISP)**: Clients should not be forced to depend on interfaces they don't use. Prefer small, focused interfaces over fat ones.
5. **Dependency Inversion Principle (DIP)**: Depend on abstractions, not concretions. High-level modules should not depend on low-level modules; both should depend on abstractions.

**Application Rule**: Before writing or modifying ANY code, ask:
- "Is this the simplest thing that could possibly work?" (KISS)
- "Does this violate any SOLID principle? If so, why is the trade-off justified?"
- "Would a future maintainer thank me for this decision?"

---

## Core Principles

1. **Preserve Behavior Exactly**: Inputs, outputs, side effects, error semantics, and edge-case guards must remain identical. Existing tests must pass without modification.
2. **Clarity Over Cleverness (and Over Brevity)**: The goal is human comprehension, not fewer lines. Never replace readable code with cryptic one-liners, nested ternaries, or dense chains.
3. **Direct Over Indirection**: Do not take three hops to do something simple. Inline trivial single-use wrappers, pass-through helpers, and speculative abstractions that add ceremony without clarity.
4. **Chesterton's Fence**: Before deleting or rewriting code that looks redundant or defensive, understand *why* it was written (check callers, tests, comments, or `workflow/<change-slug>/SPEC.md`).

---

## When to Apply

- Right after a feature or bug fix works and tests pass, to clean up rough or over-complicated implementation details.
- When functions have deep `if/else` nesting, long monolithic blocks, or unclear variable/method names.
- When `workflow/<change-slug>/REVIEW.md` flags readability, maintainability, or over-engineering issues.
- **Always** when reviewing code against KISS and SOLID—these are the baseline standards.

---

## What to Simplify (with KISS & SOLID Lens)

- **Flatten control flow**: Replace deep `if/else` nesting with guard clauses and early returns. (SRP: each branch has one responsibility)
- **Remove unnecessary indirection**: Collapse pass-through functions, single-use factories, or redundant layers that hide simple operations. (KISS: no speculative abstractions)
- **Make logic linear and objective**: Replace convoluted state flags or roundabout transformations with direct, step-by-step code. (KISS + SRP)
- **Right-size functions**: Break up overly long functions into cohesive, well-named units—only when each unit represents a clear step, not to fragment trivial code. (SRP + ISP)
- **Use clear names, trim obvious noise**: Rename vague identifiers so code is self-explanatory. Remove comments that merely restate the code, while preserving non-obvious *why* comments and existing docstrings. (KISS)
- **Unify genuine duplication**: When two blocks repeat the same logic, extract a single parameterized helper rather than maintaining parallel copies. (DRY, but only when it *simplifies*—KISS first)
- **Enforce single responsibility**: If a function/class does multiple things, split it. Each unit should have one reason to change. (SRP)
- **Depend on abstractions**: Replace concrete dependencies with interfaces/protocols where it enables testing and flexibility without over-engineering. (DIP + KISS balance)
- **Prefer composition over inheritance**: Avoid deep inheritance hierarchies. Use composition and delegation. (LSP + OCP)
- **Keep interfaces small and focused**: Don't force clients to implement methods they don't need. (ISP)

---

## Guardrails (What NOT to Do)

- Do **not** alter public APIs, return shapes, or error handling unless explicitly requested.
- Do **not** refactor unrelated files outside the scope of the current change.
- Do **not** remove edge-case checks (`null`, empty, boundary conditions) unless proven unreachable.
- Do **not** introduce abstractions, patterns, or indirection "for future flexibility" without a concrete, current need (YAGNI — KISS enforcement).
- Do **not** violate SOLID principles without a documented, justified trade-off in the change record.

---

## Verification

1. Review the diff to confirm every change is simpler, cleaner, and strictly behavior-preserving.
2. Run the relevant test suite, linter, and typechecker to verify all checks pass unchanged.
3. **KISS/SOLID Check**: For each changed unit, verify:
   - Does it have a single, clear responsibility? (SRP)
   - Is it open for extension without modification? (OCP)
   - Are subtypes safely substitutable? (LSP)
   - Are interfaces minimal and focused? (ISP)
   - Does it depend on abstractions, not concretions? (DIP)
   - Is this the simplest solution that works? (KISS)