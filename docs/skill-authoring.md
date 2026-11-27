# Skill Authoring Standard

> Applies to every `skills/<name>/SKILL.md` and its `resources/`.
> This is a repository standard, not an agent skill. It is enforced by the
> structural validator (B1a) and referenced by `spec-driven-workflow`.

## 1. One Skill, One Job

- A skill owns exactly one responsibility.
- If the description needs "and" to explain the job, split the skill.
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

## 3. Size Budget

- `SKILL.md`: target **≤ 150 lines**, hard cap **250**.
- Move templates, rulebooks, long tables, and examples to `resources/`.
- The core file must be usable without opening a resource; resources are loaded
  on demand.
- If a skill exceeds the cap, move content to `resources/` or split the skill.

> **Known exception**: `software-audit` (263 lines) is the only skill above the
> cap. B5 removes its duplicated KISS/SOLID block and brings it under 250.

## 4. Structure

Use the sections that fit the skill; omit empty ones.

1. **Title + purpose** — one paragraph.
2. **When to use / When not to use** — concrete triggers.
3. **Core rules** — the non-negotiables.
4. **Workflow** — ordered steps.
5. **Output / verification** — what "done" looks like.
6. **Guardrails / anti-patterns** — what not to do.
7. **References** — canonical skills and resources.

## 5. Reference, Don't Repeat

- Define a concept **once**, in its canonical skill.
- Other skills point to it by name: "Apply the KISS/SOLID heuristics in
  `code-simplification`."
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
- [ ] All relative links resolve.
- [ ] `SKILL.md` is ≤ 150 lines (warn) and ≤ 250 (fail).
- [ ] The canonical doctrine is defined once (no duplicated KISS/SOLID blocks).
- [ ] No secrets, absolute local paths, or proprietary references.
- [ ] The skill follows the section structure above.

## 10. Anti-Patterns

| Anti-pattern | Why it hurts | Instead |
|---|---|---|
| Two jobs in one skill | Unclear trigger; bloated context | Split |
| Restating KISS/SOLID | Drift; token cost | Reference `code-simplification` |
| 500-line `SKILL.md` | Context bloat | Move to `resources/` |
| Broad description | Over-triggering | Concrete trigger + negative trigger |
| Absolute claims | False authority | Evidence or "often" |
| Credential list | Tokens without behavior | Operational standards only |

## 11. Definition of Done

A skill change is done when:

- [ ] It passes the structural checks (B1a).
- [ ] The canonical doctrine is referenced, not duplicated.
- [ ] A reader can answer "when do I use this?" from the description alone.
- [ ] The change is recorded in the changelog (once B15 lands).

## Relationship to Other Artifacts

- **Design doctrine**: `code-simplification` (KISS master, SOLID contextual).
- **Process**: `spec-driven-workflow`.
- **Audit**: `software-audit`.
- **Enforcement**: the structural validator (B1a).
