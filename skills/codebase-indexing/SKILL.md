---
name: codebase-indexing
description: >-
  Index, synchronize, and query a project's codebase using CodeGraph and
  Repomix. Use this skill when exploring an unfamiliar repository, maintaining
  up-to-date code intelligence after edits, or setting up project indexing with
  `.codegraph/` and Markdown-formatted Repomix snapshots.
---

# Codebase Indexing (CodeGraph + Repomix)

Maintain a dual indexing setup and **use these indexed artifacts as your primary context** to understand the project's full structure, architecture, and engineering before scanning individual files or running brute-force searches:
- **CodeGraph** (`.codegraph/`) as the primary source for structural symbol lookup, call paths, and dependency graphs.
- **Repomix** (`repomix-output.md`) as the primary single-file Markdown snapshot of the repository's layout and implementation.

> **Local index rule**: both indexes are **local, persistent working artifacts**. Keep them on disk (never delete them during cleanup), but **do not version them**. CodeGraph creates `.codegraph/.gitignore` by default (keep that behavior), and the Repomix output should be added to the project's `.gitignore`. In a distributed team, different developers, IDEs, agents, and branches would otherwise produce constant merge conflicts over generated indexes.

Always check whether each index already exists before running commands: **initialize** when missing, and **sync/update** when present.

## 0. Docs-Only / No-Supported-Source Mode

Before running any CodeGraph command, run a lightweight pre-check: glob for known source extensions (e.g. `*.ts, *.js, *.py, *.go, *.rs, *.java, *.cs, *.php, *.rb`) and common manifest files (`package.json`, `go.mod`, `Cargo.toml`, `pyproject.toml`, `pom.xml`) at the repository root, excluding `node_modules/`, `.git/`, `dist/`, `build/`, `vendor/`, `.codegraph/`.

- **No candidate source files found** → the repo is docs-only. Skip CodeGraph entirely, mark CodeGraph `N/A`, and proceed to `## 2. Repomix`. Repomix is the primary index in docs-only mode.
- **Candidates exist but uncertain** → proceed to `## 1. CodeGraph` Pre-flight Check and run `codegraph init`. If it reports "No supported source files found" (or equivalent), mark CodeGraph `N/A`, skip `codegraph index`, and proceed to `## 2. Repomix`.
- **`codegraph init` succeeds** → continue with the normal Check & Initialize vs. Sync flow (`## 1. CodeGraph`).

**Persistent index policy (always)**: an existing `.codegraph/` is never deleted. If `.codegraph/` exists from a prior run (e.g., the repo later gains source files), it is preserved and `codegraph sync` is attempted.

## 1. CodeGraph

### Pre-flight Check

Before running any CodeGraph command, verify that the `codegraph` executable is available in the environment (`command -v codegraph`).

- **If `codegraph` is NOT installed**:
  - **Alert the user immediately** that CodeGraph indexing cannot be executed because the `codegraph` executable is missing from the environment.
  - **Provenance verification (required before any install)**:
    1. Check npm registry: `npm view @colbymchenry/codegraph` — confirm package exists, note latest version.
    2. Verify maintainer: `npm view @colbymchenry/codegraph maintainers` — must be `@colbymchenry` (Colby McHenry).
    3. Verify repository: `npm view @colbymchenry/codegraph repository.url` — must point to `github.com/colbymchenry/codegraph` (or expected source).
    4. Check downloads: `npm view @colbymchenry/codegraph downloads` — weekly downloads should be reasonable (not a typo-squat).
    5. Optional: run `npm audit @colbymchenry/codegraph` for known vulnerabilities.
  - **If verification FAILS** (package missing, maintainer mismatch, suspicious repo/downloads):
    - **Do not install**. Warn the user with the specific failure reason.
    - Continue with Repomix/plain reads. Mark CodeGraph `N/A` until resolved.
  - **If verification PASSES**:
    - Record the verified version: `CODEGRAPH_VERSION="<version>"` (e.g., `1.2.3`).
    - Document in skill: `> Verified: @colbymchenry/codegraph@<version> from npm registry, maintainer @colbymchenry, repo github.com/colbymchenry/codegraph, checked on <YYYY-MM-DD>.`
    - **Preferred install path** (choose one):
      - **One-off / no project lock-in**: `npx -y @colbymchenry/codegraph@${CODEGRAPH_VERSION}`
      - **Project-local** (recommended for repos using CodeGraph regularly):
        ```bash
        npm install --save-dev @colbymchenry/codegraph@${CODEGRAPH_VERSION}
        # then use: npx codegraph
        ```
      - **Global fallback ONLY with justification**: if the user explicitly needs global availability (multiple projects, CI without `npx`):
        ```bash
        npm install -g @colbymchenry/codegraph@${CODEGRAPH_VERSION}
        # Justification: <user-provided reason>
        ```
    - Optional: configure MCP / AI agent integration with pinned version:
      ```bash
      npx -y @colbymchenry/codegraph@${CODEGRAPH_VERSION} install
      ```
    - Initialize and index the project:
      ```bash
      codegraph init
      codegraph index
      ```
  - Do not silently ignore the missing binary; continue with Repomix (if available) and note that CodeGraph remains unindexed until installed.

### Check & Initialize vs. Sync

Perform a **two-part check** at the repository root: verify both that the `.codegraph/` directory exists **and** that the SQLite graph database inside it (`.codegraph/codegraph.db`) exists (`[ -d .codegraph ] && [ -s .codegraph/codegraph.db ]`):

- **If `.codegraph/` does NOT exist** — initialize and build the graph database:
  ```bash
  codegraph init
  codegraph index
  ```
- **If `.codegraph/` exists, but `.codegraph/codegraph.db` is missing or empty** — the directory alone is not a usable index; build the database (run `codegraph init` first if `.codegraph/` is empty or lacks configuration):
  ```bash
  codegraph index
  ```
- **If BOTH `.codegraph/` and `.codegraph/codegraph.db` exist** — always run incremental synchronization:
  ```bash
  codegraph sync
  ```
  Do not re-run `codegraph init` or a full `codegraph index` when both `.codegraph/` and `.codegraph/codegraph.db` are already present, unless the database is corrupted or major structural resets require a rebuild.

### Querying CodeGraph

Reach for CodeGraph before running brute-force `grep`/`find` across the codebase.

**CLI (preferred, always portable when the binary is installed):**

```bash
codegraph explore "<symbol names, file paths, or architectural question>"
```

**MCP tool (when the agent exposes it):** the exposed name depends on the agent/harness, so **discover it instead of hardcoding one name**.

| Agent / Harness | Typical exposed name |
| :--- | :--- |
| **OpenCode** | `codegraph_codegraph_explore` — server key + tool name (`<server>_<tool>`) |
| **Claude Code** | `mcp__codegraph__codegraph_explore` — `mcp__<server>__<tool>` (may be `mcp__codegraph__explore` if the server registers the tool as `explore`) |
| **Other MCP clients** | Any tool whose name contains `codegraph` and `explore` (e.g., `*codegraph*explore*`) |

**Discovery rule**: list the tools available in the current agent and pick the match for CodeGraph exploration. If none is exposed, or you are unsure, **fall back to the CLI** (`codegraph explore`) rather than concluding that CodeGraph is unavailable. Never call a hardcoded MCP tool name that the current agent does not expose.

## 2. Repomix (Markdown Priority)

Always prioritize **Markdown** (`--style markdown`) for Repomix outputs so the snapshot is readable by both humans and LLMs. Always invoke Repomix via `npx -y` so it executes reliably even when not installed globally and without interactive prompts.

### Check & Create vs. Update

Check if a Repomix Markdown file (e.g., `repomix-output.md`, or the output path configured in `repomix.config.json`) exists:

- **If it does NOT exist** — create it in Markdown format:
  ```bash
  npx -y repomix --style markdown -o repomix-output.md
  ```
- **If it already exists** — re-run the update command to synchronize it with the current codebase:
  ```bash
  npx -y repomix --style markdown -o repomix-output.md
  ```
  *(If `repomix.config.json` is present and already configured with `"style": "markdown"`, running `npx -y repomix` updates the configured output file directly.)*

## 3. Maintenance & Verification

1. **Keep indexes fresh**: Run `codegraph sync` and update the Repomix Markdown file after significant code changes, branch switches, or merges.
2. **Keep indexes local but persistent**: `.codegraph/` and the Repomix output are local code-intelligence artifacts. Never delete them during cleanup, but do not version them. Respect CodeGraph's generated `.codegraph/.gitignore` (it ignores the database and transient files), and add the Repomix output (e.g., `repomix-output.md`) to the project's `.gitignore` — ask the user for confirmation before editing `.gitignore` if the entry is missing. Rationale: distributed teams on different branches, IDEs, and agents would otherwise fight merge conflicts over generated indexes.
3. **Verify**: Confirm `.codegraph/` responds to `codegraph explore` and `repomix-output.md` (or the configured `.md` file) reflects the latest file tree.
