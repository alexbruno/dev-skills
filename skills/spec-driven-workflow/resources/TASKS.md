# Tasks: <Short Title>

- **Plan**: [PLAN.md](./PLAN.md)
- **Execution Mode**: Sequential | Parallel / Multi-Agent

> Use this file only when the plan is large enough to require sharding or when tasks can be executed asynchronously by separate agents.

## Task Board

| ID | Task | Type | Status | Owner / Agent | Dependencies | Scope (Files/Modules) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T1 | <Short task title> | `feature` | `pending` | <agent-1> | None | `path/to/scope_a` |
| T2 | <Short task title> | `docs` | `pending` | <agent-2> | None | `path/to/scope_b` |
| T3 | <Integration / verification> | `chore` | `pending` | <agent-1> | T1, T2 | `tests/` |

## Task Details

### T1: <Short task title>

- **Status**: `pending` | `in_progress` | `blocked` | `done`
- **Dependencies**: None
- **Scope**: `path/to/scope_a`
- **Instructions**: Concise implementation instructions for the assigned agent.
- **Done Criteria**:
  - [ ] <Specific check or test passing>

### T2: <Short task title>

- **Status**: `pending` | `in_progress` | `blocked` | `done`
- **Dependencies**: None
- **Scope**: `path/to/scope_b`
- **Instructions**: Concise implementation instructions for the assigned agent.
- **Done Criteria**:
  - [ ] <Specific check or test passing>
