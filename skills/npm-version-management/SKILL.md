---
name: npm-version-management
description: >-
  Enforce disciplined NPM version management via `npm version` CLI. Use when
  publishing or versioning an NPM package.
---

# NPM Version Management

Strict policy for managing NPM package versions in Node.js/JS/TS projects.

## The Cardinal Rule: Never Manually Edit `package.json` Version

> [!CAUTION]
> **NEVER manually edit the `"version"` field in `package.json` using file editing tools, text editors, or search-and-replace scripts.**
>
> All version bumps MUST be executed strictly through the official `npm version` command.

Git tags, CI/CD pipelines, release tooling, and package registries depend on `npm version`'s atomic update of `package.json`, commit, and annotated tag. Manual edits break this synchronization, causing version drift, pipeline failures, and lost release tracking.

## Pre-Bump Checklist

Before running `npm version`:

1. **Clean Working Tree**: `git status --porcelain` must be empty. Commit or stash changes; never bypass with `--force`.
2. **Verification & Tests Pass**: All tests, typechecks, linters green.
3. **Correct Branch**: On designated release branch (e.g., `main`, `master`, `release/*`).

## Versioning Commands & SemVer Selection

| Command | SemVer Level | When to Use | Example (`1.0.0` -> ) |
| :--- | :--- | :--- | :--- |
| `npm version patch` | **PATCH** | Backward-compatible bug fixes | `1.0.1` |
| `npm version minor` | **MINOR** | Backward-compatible new features | `1.1.0` |
| `npm version major` | **MAJOR** | Breaking changes, incompatible API | `2.0.0` |

- **Pre-release**: `npm version prepatch --preid=alpha` (etc.); increment with `npm version prerelease`.
- **Custom message**: `npm version minor -m "Release %s: description"`.
- **Specific target**: `npm version 2.4.0`.

## NPM Lifecycle Hooks & Remote Sync

Use `preversion` (test/lint), `version` (build, `git add dist/`), `postversion` (push + tags) in `package.json` scripts. After `npm version`, push: `git push && git push --tags`.

## Drift Detection & Remediation

When inspecting a repo before release/build, verify `package.json` version matches latest Git tag.

### Detecting Drift

1. **Uncommitted version edit**: `git diff package.json` shows unstaged/staged `"version"` changes.
2. **Missing/mismatched tag**: Extract version `X.Y.Z` from `package.json`; check `git tag -l "vX.Y.Z"`. If tag missing or points to wrong commit, drift detected.

### Remediation Workflows

**Option 1: Revert Manual Edit and Use `npm version` (Recommended)**

- *Uncommitted edit*:
  ```bash
  git checkout HEAD -- package.json package-lock.json
  npm version <patch|minor|major|X.Y.Z>
  ```
- *Committed locally (HEAD)*:
  ```bash
  git reset --soft HEAD~1
  git checkout HEAD -- package.json package-lock.json
  npm version <patch|minor|major|X.Y.Z>
  ```

**Option 2: Retroactively Synchronize Git Tag**

1. `npm install --package-lock-only` (sync lockfile)
2. `git add package.json package-lock.json && git commit -m "vX.Y.Z"` (if uncommitted)
3. `git tag -a "vX.Y.Z" -m "vX.Y.Z"`
4. `git push && git push origin "vX.Y.Z"`

## Agent Guardrails & Interception

1. **Intercept Manual Edits**: If asked to "change version to X" or "bump version in package.json", do NOT use file tools on `package.json` for `"version"`. Run `npm version <...>` via shell.
2. **Execute Terminal Command**: Use `npm version <major|minor|patch|version>` via shell execution.
3. **Detect & Alert on Drift**: When inspecting repo before release/build, verify `package.json` version matches Git tags. If manual edit or missing tag detected:
   - Alert user proactively.
   - Explain `package.json` and Git tags are out of sync.
   - Offer both remediation paths.
4. **Notify User**: Clearly state `npm version` was used to ensure synchronization between `package.json` and Git version tags.
