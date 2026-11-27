---
name: unfamiliar-stack-onboarding
description: >-
  Learn the local idiom of an unfamiliar stack and prove a working slice
  before abstracting. Use when starting work in a repository with an
  unfamiliar language, framework, runtime, or repo conventions. Do not use
  for familiar stacks or single-file scripts.
---

# Unfamiliar Stack Onboarding

When entering a codebase with a language/framework/runtime you don't know
idiomatically, don't transpose patterns from other stacks. Instead, learn the
local conventions, establish the fastest feedback loop, and prove a walking
skeleton before any abstraction.

## When to Use / When Not to Use

- **Use when**: starting work in a repository with an unfamiliar language,
  framework, runtime, or repo conventions (post-`environment-preflight` or
  when `environment-preflight` reveals a stack you don't know idiomatically).
- **Do not use for**: familiar stacks (you know the idioms); single-file
  scripts; prototyping without a repo context.

## Core Rules

- **Stack-agnostic**: this skill teaches *how to learn* the local idiom, not
  the idiom itself. No language/framework-specific advice.
- **Fastest loop first**: identify and verify the project's build/test/lint
  cycle before writing any abstraction.
- **Walking skeleton before abstraction**: prove an end-to-end working slice
  (hello-world through test) before introducing any pattern.
- **Anti-cargo-culting**: every non-trivial pattern must cite local idiom/docs
  as justification; "we did it this way in <other stack>" is not a
  justification.

## Workflow (4 Steps)

### 1. Idiomatic Reconnaissance

- Read manifests/lockfiles/configs at the repo root (and workspace packages if
  monorepo) to map the stack: runtimes, package managers, frameworks, test
  runners, linters.
- Open **official docs for the pinned versions** (not latest) — e.g., Go
  `go.mod` version, Node `engines`/`package.json`, Rust `rust-toolchain.toml`.
- Find **2–3 local reference files**: tests, examples, entry points, or config
  files that show the project's style (naming, error handling, async patterns,
  dependency injection, test structure).

### 2. Fastest Loop

- Identify the **single command** (or minimal subset) that runs build + test +
  lint (or the subset the project uses for CI).
- Run it and **confirm it passes** on the current codebase.
- If the loop is slow, note the bottleneck but do not skip verification.

### 3. Walking Skeleton

- Write the **smallest end-to-end slice** that exercises the fastest loop:
  add a trivial test, run it, watch it pass.
- No abstractions, no helpers, no generalization — just the local idiom.
- The skeleton must be runnable via the fastest loop and produce a passing
  result.

### 4. Anti-Cargo-Culting Gate

- Before any abstraction/helper/generalization, write a **one-line
  justification** citing the local idiom/doc that motivates it:
  > Justification: <local file or doc> uses <pattern> because <reason>.
- If no local justification exists, **do not add the abstraction**.

## Output / Verification

- **Done when**: the fastest loop is identified and passing; a walking
  skeleton test runs end-to-end through that loop; any proposed abstraction
  has a local-idiom justification.

## Guardrails / Anti-Patterns

- **Transposing patterns**: don't write TS-style DI in Go, or OOP factories in
  Rust, without local justification.
- **Abstraction before verification**: no helper/util/base class until the
  walking skeleton passes through the fastest loop.
- **Skipping the loop**: if the test command is unknown, find it first — don't
  guess.
- **Vague justification**: "cleaner" or "standard practice" is not a
  justification; cite a local file or pinned-version doc.

## References

- `environment-preflight` — identifies the stack and verifies tool access; may
  surface an unfamiliar stack.
- `docs/skill-authoring.md` — B21 structure and trigger standards.
