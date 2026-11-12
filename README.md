# dev-skills

Curated agent skills for AI spec-driven development, sequential reasoning, and pragmatic software engineering.

> **Repository Convention**: All skills, templates, and documentation in this repository are written in **English** so they can be shared and installed universally across any codebase.

## Available Skills

| Skill | Description |
| :--- | :--- |
| [`code-review`](./skills/code-review/SKILL.md) | Structured, five-axis code review aligned with `spec-driven-workflow` (`SPEC.md`, `PLAN.md`, `REVIEW.md`). |
| [`code-simplification`](./skills/code-simplification/SKILL.md) | Simplify code for human readability, clarity, and directness without changing behavior. |
| [`codebase-indexing`](./skills/codebase-indexing/SKILL.md) | Index, synchronize, and query a codebase using CodeGraph (`.codegraph/`) and Markdown-formatted Repomix snapshots. |
| [`decision-records`](./skills/decision-records/SKILL.md) | Document significant architectural and technical trade-offs as lightweight ADRs. |
| [`incremental-implementation`](./skills/incremental-implementation/SKILL.md) | Build features and refactorings in small, verified vertical slices aligned with `PLAN.md` and `TASKS.md`. |
| [`planning-and-task-breakdown`](./skills/planning-and-task-breakdown/SKILL.md) | Stack-agnostic execution planning and task decomposition aligned with `SPEC.md`, `PLAN.md`, and conditional `TASKS.md`. |
| [`spec-driven-workflow`](./skills/spec-driven-workflow/SKILL.md) | Default software engineering workflow using a `workflow/` directory (`SPEC.md`, `PLAN.md`, `TASKS.md`, and `REVIEW.md`) to specify, plan, execute, and review changes. |
| [`systematic-debugging`](./skills/systematic-debugging/SKILL.md) | Root-cause analysis, regression testing, and disciplined debugging before applying fixes. |
| [`verification-and-testing`](./skills/verification-and-testing/SKILL.md) | Evidence-based verification gates and meaningful test coverage aligned with `SPEC.md` acceptance criteria. |

## Installation

Install all skills or a specific skill into any project using your preferred agent skills workflow:

### Using `skills` CLI

```bash
npx skills add alexbruno/dev-skills
```

### Manual Installation

Copy the desired skill directory from `skills/` into your project's agent skills folder (for example, `.agents/skills/` or `.claude/skills/`):

```bash
mkdir -p .agents/skills
cp -r skills/spec-driven-workflow .agents/skills/
```
