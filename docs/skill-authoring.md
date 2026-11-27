# Skill Authoring Standard

> Applies to every `skills/<name>/SKILL.md` and its `resources/`.
> This is a repository standard, not an agent skill. It is enforced by the
> structural validator (B1a) and will be referenced by `spec-driven-workflow` (B5).

## 1. One Skill, One Job

- A skill owns exactly one responsibility.
- If the description joins two **unrelated** jobs, split the skill. A single job
  may legitimately contain "and": "readability, clarity, and directness" is one
  responsibility; "author skills and run audits" is two.
- If two triggers produce different workflows, split.
- Resources belong to their skill; they are not separate skills.

## 2. Frontmatter

Every `SKILL.md` starts with:

```yaml
---
name: kebab-case-name
description: >-
  One or two sentences: what the skill does, when to use it, and (when useful)
  when not to use it.
---
```

Rules:

- `name` is kebab-case and matches the directory exactly.
- `description` is English, 1-2 sentences, with a concrete trigger.
- Add a **negative trigger** when over-activation is likely (e.g., "Do not use
  for typo-only edits").
- Keep it under ~600 characters; never exceed the platform limit.
- No extra fields unless the target platform requires them.

## 3. Size Budget & Progressive Disclosure

- `SKILL.md`: target **≤ 150 lines**, hard cap **250**.
- Move templates, rulebooks, long tables, and examples to `resources/`.
- The core must be self-sufficient to **decide and start**: purpose, trigger,
  ordered steps, and done criteria.
- A step that depends on a resource names it at that step ("Read
  `resources/x.md` before …"). A resource mentioned only in an appendix is a
  resource the agent will skip.
- No essential decision may depend on a resource the core does not name.
- If a skill exceeds the cap, move content to `resources/` or split the skill.

> **Known exceptions**: `software-audit` is above the 250 hard cap until B5
> moves its *Nine Dimensions* section to `resources/audit-dimensions.md`.
> `npm-version-management` (220) and `spec-driven-workflow` (156) are above the
> 150 target; B12 compacts npm, and `spec-driven-workflow` is a documented
> warning.

## 4. Structure

Use the sections that fit the skill; omit empty ones.

1. **Title + purpose** — one paragraph.
2. **When to use / When not to use** — concrete triggers.
3. **Core rules** — the non-negotiables.
4. **Workflow** — ordered steps.
5. **Output / verification** — what "done" looks like.
6. **Guardrails / anti-patterns** — what not to do.
7. **References** — canonical skills and resources (an index; a step that
   depends on a resource must still name it inline, per §3).

## 5. Reference, Don't Repeat

- Define a concept **once**, in its canonical skill.
- Other skills point to it using the canonical vocabulary approved in B3:
  "Apply the KISS-first doctrine in `code-simplification`: KISS is the master
  philosophy, SOLID is a contextual heuristic for object-oriented design, and
  KISS/YAGNI win ties."
- Cross-skill references are for **canonical concepts** (doctrine, workflow
  phases, review criteria). They assume the full skill set is installed.
- When a skill must work standalone (the README supports installing one skill),
  keep a **one-line operational summary** and label it as a summary; the
  canonical definition still lives in exactly one place.
- **Resources travel with their skill; cross-skill references do not.** For
  procedures that must always be available, prefer the skill's own `resources/`.
- Do not restate the five SOLID principles, the same checklist, or the same
  table in multiple skills.
- Use backticks for skill names and relative links for resources.

## 6. Language & Tone

- **English** (repository convention).
- Imperative, direct, evidence-based.
- Avoid absolute claims ("inevitably", "always") unless true by construction.
- Avoid "credential theater": name a standard only if the skill uses it
  operationally.
- No secrets, credentials, absolute local paths, or client/proprietary
  references.

## 7. Triggers

- The `description` is the activation mechanism.
- Name the task, artifact, or symptom — not "any project".
- Prefer a narrow trigger plus a negative trigger over a broad claim.
- Test the description against a real task: would a reasonable agent activate
  it here?

## 8. Resources

- `resources/` holds templates, checklists, and long reference material.
- Link resources with relative paths from `SKILL.md`.
- Resources have no frontmatter requirement.
- Use kebab-case, descriptive filenames.

## 9. Verification

The B1a validator codifies these checks:

- [ ] `name` matches the directory.
- [ ] `description` is present, English, and within the length limit.
- [ ] All relative links resolve. The checker ignores template placeholders
  (`<...>`, `XXXX-*`) and context-relative examples.
- [ ] `SKILL.md` is ≤ 150 lines (warn) and ≤ 250 (fail).
- [ ] A non-canonical skill does **not** define two or more of
  `SRP/OCP/LSP/ISP/DIP` or restate a principle's definition; pointers to
  `code-simplification` are allowed and expected.
- [ ] Documented counts match their declared source: the number of skills comes
  from `skills/*/SKILL.md`, the nine dimensions from
  `skills/software-audit/resources/audit-dimensions.md` (created by B5), and the
  README matches both.
- [ ] No secrets, absolute local paths, or proprietary references.
- [ ] The skill has a purpose paragraph, a trigger (a "When to use/apply/create"
  section, or a `description` containing a concrete "Use when..." clause), and a
  verification/output section. The rest of §4 is advisory.

> **Ratchet**: a new violation fails the build; an existing violation is a
> warning until that skill is edited. Every skill edited by an M1 item must meet
> the minimum in the same change, and the committed baseline may only shrink.

## 10. Anti-Patterns

| Anti-pattern | Why it hurts | Instead |
|---|---|---|
| Two jobs in one skill | Unclear trigger; bloated context | Split |
| Restating KISS/SOLID | Drift; token cost | Reference `code-simplification` |
| 500-line `SKILL.md` | Context bloat | Move to `resources/` |
| Broad description | Over-triggering | Concrete trigger + negative trigger |
| Absolute claims | False authority | Evidence or "often" |
| Unverified cross-reference | Credibility | Verify it or mark it as future work |
| Credential list | Tokens without behavior | Operational standards only |

## 11. Definition of Done

A skill change is done when:

- [ ] It passes the structural checks (B1a).
- [ ] It meets the §9 minimum, and the ratchet baseline did not grow.
- [ ] The canonical doctrine is referenced, not duplicated.
- [ ] A reader can answer "when do I use this?" from the description alone.
- [ ] The change is recorded in the changelog (once B15 lands).

## Relationship to Other Artifacts

- **Design doctrine**: `code-simplification` (KISS master, SOLID contextual).
- **Process**: `spec-driven-workflow`.
- **Audit**: `software-audit`.
- **Enforcement**: the structural validator (B1a).
