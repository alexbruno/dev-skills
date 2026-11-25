# Software Audit Report

> **Project**: <repo name>
> **Audit ID**: AUDIT-<YYYYMMDD>-<scope-slug>
> **Auditor**: <agent/human identifier>
> **Date**: <ISO 8601>
> **Scope**: <Full Repo | Change: workflow/<slug> | Artifact: SPEC.md/PLAN.md/etc.>
> **Workflow Phase**: <Backlog | Spec | Plan | Tasks | Implement | Review | Validation | Continuous>

---

## Executive Summary

- **Overall Risk Rating**: 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low / ⚪ Clean
- **Total Findings**: <C> Critical, <H> High, <M> Medium, <L> Low, <I> Info
- **Top 3 Risks**:
  1. <One-line risk with business impact>
  2. <One-line risk with business impact>
  3. <One-line risk with business impact>
- **Recommendation**: Go / No-Go / Conditional Go (with blockers: <list>) / Defer

---

## Context & Scope

- **Trigger**: <Scheduled | Pre-merge | Post-incident | PO request | Architect request | Continuous>
- **Artifacts Reviewed**:
  - <artifact 1> — <link>
  - <artifact 2> — <link>
- **Repository Snapshot**: Commit `<SHA>`, branch `<branch>`, key versions `<node/go/java/etc.>`
- **Code Intelligence Indexes** (per `codebase-indexing` skill):
  - **CodeGraph**: `<indexed @ <timestamp> | stale — synced @ <timestamp> | missing — rebuilt @ <timestamp>>` — DB: `.codegraph/codegraph.db` `<size>`
  - **Repomix**: `<updated @ <timestamp> | stale — refreshed @ <timestamp> | missing — created @ <timestamp>>` — File: `repomix-output.md` `<size>`
  - **Index Commit SHA**: `<SHA at index time>` (may differ from audit commit if changes during audit)
- **Assumptions & Limitations**:
  - <What was NOT audited and why>
  - <Tooling limitations (e.g., SAST not run on generated code, CodeGraph unavailable)>

---

## Findings by Dimension

### 🔴 Critical Findings

#### AUDIT-<YYYYMMDD>-001 — [<Dimension>] — <Concise Title>
- **Location**: `<file/path.ext:L<start>-L<end>>` / `<workflow/<slug>/ARTIFACT.md#section>`
- **Severity**: Critical
- **Discovery Method**: `<CodeGraph query: "..." | Repomix pattern: "..." | Manual review | SAST tool: <tool> | SCA tool: <tool> | Linter: <tool>>`
- **Evidence**:
  ```<language>
  <code snippet or spec excerpt demonstrating the issue>
  ```
- **Impact**: <Business/technical consequence if unaddressed — quantify where possible>
- **Recommendation**: <Specific, actionable remediation with code/config example>
- **References**: [CWE-XXX](https://cwe.mitre.org/data/definitions/XXX.html), [OWASP Top 10 2021:AXXX](https://owasp.org/Top10/), ADR-<NNN>
- **Status**: Open

<!-- Repeat for each Critical finding -->

### 🟠 High Findings

#### AUDIT-<YYYYMMDD>-002 — [<Dimension>] — <Concise Title>
- **Location**: `<file/path.ext:L<start>-L<end>>`
- **Severity**: High
- **Discovery Method**: `<CodeGraph query: "..." | Repomix pattern: "..." | Manual review | SAST tool: <tool> | SCA tool: <tool> | Linter: <tool>>`
- **Evidence**:
  ```<language>
  <code snippet or metric>
  ```
- **Impact**: <Consequence>
- **Recommendation**: <Remediation>
- **References**: <Links>
- **Status**: Open

<!-- Repeat for each High finding -->

### 🟡 Medium Findings

#### AUDIT-<YYYYMMDD>-003 — [<Dimension>] — <Concise Title>
- **Location**: `<file/path.ext:L<start>-L<end>>`
- **Severity**: Medium
- **Discovery Method**: `<CodeGraph query: "..." | Repomix pattern: "..." | Manual review | SAST tool: <tool> | SCA tool: <tool> | Linter: <tool>>`
- **Evidence**: <Description or snippet>
- **Impact**: <Consequence>
- **Recommendation**: <Remediation>
- **References**: <Links>
- **Status**: Open

<!-- Repeat for each Medium finding -->

### 🟢 Low Findings

#### AUDIT-<YYYYMMDD>-004 — [<Dimension>] — <Concise Title>
- **Location**: `<file/path.ext:L<start>-L<end>>`
- **Severity**: Low
- **Discovery Method**: `<CodeGraph query: "..." | Repomix pattern: "..." | Manual review | SAST tool: <tool> | SCA tool: <tool> | Linter: <tool>>`
- **Evidence**: <Description>
- **Impact**: <Minor consequence>
- **Recommendation**: <Quick fix or style improvement>
- **References**: <Links>
- **Status**: Open

<!-- Repeat for each Low finding -->

### ⚪ Informational

#### AUDIT-<YYYYMMDD>-005 — [<Dimension>] — <Concise Title>
- **Location**: `<file/path.ext>` or `<artifact.md#section>`
- **Severity**: Informational
- **Discovery Method**: `<CodeGraph query: "..." | Repomix pattern: "..." | Manual review | SAST tool: <tool> | SCA tool: <tool> | Linter: <tool>>`
- **Observation**: <Pattern noticed, suggestion, learning note>
- **Recommendation**: <Optional future-proofing idea>
- **References**: <Links>

<!-- Repeat for each Informational finding -->

---

## Cross-Cutting Themes & Systemic Patterns

> Patterns observed across multiple findings (e.g., "Missing input validation in 5 endpoints", "No ADRs for 3 architectural decisions", "Test coverage < 40% in security-sensitive modules", "Inconsistent error handling across 12 services")

| Theme | Affected Findings | Description | Suggested Systemic Fix |
|-------|-------------------|-------------|------------------------|
| <Theme Name> | AUDIT-..., AUDIT-... | <Description> | <Architectural/process change> |

---

## Metrics & Quantitative Assessment

| Metric | Value | Threshold | Status |
|--------|-------|-----------|--------|
| **Cyclomatic Complexity** (avg / max) | X / Y | < 10 / < 20 | ✅ / ⚠️ / ❌ |
| **Cognitive Complexity** (avg) | X | < 15 | ✅ / ⚠️ / ❌ |
| **Test Coverage** (line / branch / mutation) | X% / Y% / Z% | > 80% / > 70% / > 50% | ✅ / ⚠️ / ❌ |
| **Duplicated Lines** | X% | < 3% | ✅ / ⚠️ / ❌ |
| **SAST Findings** (Critical / High / Medium) | C / H / M | 0 / 0 / < 5 | ✅ / ⚠️ / ❌ |
| **Dependency Vulnerabilities** (Critical / High) | C / H | 0 / 0 | ✅ / ⚠️ / ❌ |
| **Outdated Dependencies** (major versions behind) | N | 0 | ✅ / ⚠️ / ❌ |
| **Architectural Fitness Functions Passing** | N / Total | 100% | ✅ / ⚠️ / ❌ |
| **ADR Coverage** (key decisions documented) | X% | > 80% | ✅ / ⚠️ / ❌ |
| **Secrets Detected in History** | N | 0 | ✅ / ❌ |
| **License Compliance Issues** | N | 0 | ✅ / ❌ |

---

## Static Analysis & Code Quality Findings (Sonar/Lint-Style)

> This section captures automated-tool-equivalent findings from manual code examination.
> Each entry mirrors a rule violation that a linter/SAST would flag.

| Rule ID | Rule Name | Location | Severity | Message | Snippet |
|---------|-----------|----------|----------|---------|---------|
| <ESLINT_RULE> | <rule-name> | `file.ts:42` | Major | <Description> | `<code>` |
| <SONAR_RULE> | <rule-key> | `file.java:15` | Critical | <Description> | `<code>` |
| <CUSTOM> | <description> | `file.go:88` | Minor | <Description> | `<code>` |

**Common Rule Categories Examined:**
- **Security**: Hardcoded secrets, SQL injection, XSS, path traversal, weak crypto, insecure deserialization
- **Reliability**: Null dereference, resource leaks, exception swallowing, race conditions, unhandled promises
- **Maintainability**: Cognitive complexity, function length, parameter count, nesting depth, duplicate code, dead code, magic numbers
- **Style/Conventions**: Naming, formatting, import order, unused imports/variables, consistent return types
- **Performance**: N+1 queries, unnecessary allocations, synchronous I/O in async context, missing pagination
- **Best Practices**: Error handling, logging, validation, documentation, testability, SOLID violations

---

## Remediation Roadmap

| Priority | Finding IDs | Owner | Target Date | Dependencies |
|----------|-------------|-------|-------------|--------------|
| P0 (Block Release) | AUDIT-..., AUDIT-... | Architect/Worker | <YYYY-MM-DD> | — |
| P1 (Next Sprint) | AUDIT-..., AUDIT-... | Worker | <YYYY-MM-DD> | — |
| P2 (Backlog) | AUDIT-..., AUDIT-... | Team | <YYYY-Q<Q>> | <Depends on...> |

---

## Sign-Off

- **Auditor**: <name> — <date> — ☐ Complete
- **Architect Review**: <name> — <date> — ☐ Acknowledged / ☐ Disputed (see comments below)
- **Product Owner Awareness**: <name> — <date> — ☐ Informed (for Critical/High business impact)

### Architect Comments (if disputed)
> <Comments on disputed findings>

---

## Appendix: Tooling Evidence (Optional)

<details>
<summary>SAST/SCA/Secrets Scan Outputs</summary>

```
<Paste relevant tool output here or link to CI artifact>
```

</details>

<details>
<summary>Architecture Analysis (dependency graphs, fitness functions)</summary>

```
<Paste or link>
```

</details>

<details>
<summary>Test Coverage Reports</summary>

```
<Paste or link>
```

</details>