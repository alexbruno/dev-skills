---
name: error-to-guardrail
description: >-
  Capture a recurring class of agent error and add the cheapest deterministic
  guardrail. Use when a recurring error class is confirmed (post-incident,
  post-review, or during debugging). Do not use for isolated bugs with no
  recurrence risk.
---

# Error to Guardrail

Convert a recurring agent error class into the cheapest deterministic guardrail
that prevents recurrence. The guardrail is a test, lint rule, typecheck config,
CI gate, config rule, or (last resort) a skill note — chosen in that exact
priority order.

## When to Use / When Not to Use

- **Use when**: a recurring class of agent error is confirmed (same obsolete API,
  wrong command, skipped check, convention violation) — post-incident, post-
  review, or during `systematic-debugging`.
- **Do not use for**: isolated bugs with no recurrence risk; one-off typos;
  non-agent errors (logic bugs in application code).

## Core Rule — Deterministic Priority Order

When a recurring error class is confirmed, add the cheapest guardrail in this
exact order:

1. **Test / Lint / Typecheck / CI** — add/adjust a test, lint rule, typecheck
   config, or CI gate that fails on the pattern.
2. **`AGENTS.md` / config rule** — add a project-level rule in `AGENTS.md`
   (or `.cursorrules`, `.github/copilot-instructions.md`, linter config, etc.)
   that agents must follow.
3. **Skill update** — update the relevant skill with a guardrail note (last
   resort; avoids skill bloat).

Never skip a priority level because it feels easier. The order is
deterministic: automated check > config rule > documentation.

## Workflow

1. **Classify the error**: name the recurring class (e.g., "obsolete API call",
   "wrong CLI flag", "skipped lint", "convention violation").
2. **Pick the priority**: start at priority 1. If an automated check can catch
   it, implement it. If not, move to priority 2. If not, priority 3.
3. **Implement the guardrail**:
   - Priority 1: write the test/lint rule/typecheck config/CI step.
   - Priority 2: write the rule in `AGENTS.md` or relevant config file.
   - Priority 3: add a one-line guardrail note to the relevant skill.
4. **Mandatory Verification**:
   - Priority 1: run the check — it **must fail before** the guardrail and
     **pass after**. Record command + output.
   - Priority 2: run the linter/rule checker — it **must catch the pattern**.
     Record command + output.
   - Priority 3: **document the manual check** and why automation isn't viable.
5. **Record the guardrail**: add a one-paragraph record to the change/PR
   description or `docs/process/DECISIONS.md`:
   > Guardrail added for <error class>: <priority 1|2|3> — <check command> — fails before, passes after (or catches pattern).

## Output / Verification

- **Done when**: the guardrail is implemented at the lowest feasible priority,
  the mandatory verification passes (fail-before/pass-after, catch-pattern, or
  documented manual check), and the one-paragraph record is written.

## Guardrails / Anti-Patterns

- **Skipping priority 1**: don't jump to `AGENTS.md` if a test/lint rule can catch it.
- **Vague error class**: "it failed" is not a class; name the specific pattern.
- **No verification**: a guardrail without verification is not a guardrail.
- **Skill bloat**: priority 3 is last resort; prefer automated checks.

## References

- `systematic-debugging` — root-cause debugging that may surface recurring classes.
- `docs/skill-authoring.md` — B21 structure and trigger standards.
