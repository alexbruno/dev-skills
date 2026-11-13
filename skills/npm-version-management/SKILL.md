---
name: npm-version-management
description: >-
  Enforce disciplined NPM package version management strictly via the `npm
  version` CLI. Prohibit manual edits to the `version` field in `package.json`,
  detect version drift between `package.json` and Git tags, and provide clear
  remediation steps to restore synchronization.
---

# NPM Version Management

Define strict policies and workflows for managing and updating NPM package versions in Node.js and JavaScript/TypeScript projects.

---

## The Cardinal Rule: Never Manually Edit `package.json` Version

> [!CAUTION]
> **NEVER manually edit the `"version"` field in `package.json` using file editing tools, text editors, or search-and-replace scripts.**
>
> All version bumps MUST be executed strictly through the official `npm version` command.

---

## Why Git Tag Synchronization Matters

In a Git repository, the `npm version` command performs three critical atomic operations:

1. **Updates Manifests**: Safely bumps the `"version"` field in `package.json` and updates `package-lock.json` (preserving formatting and indentation).
2. **Creates a Git Commit**: Stages the manifest changes and generates a dedicated release commit with a standardized message (default: `v<version>`).
3. **Creates a Git Tag**: Creates an annotated Git tag matching the new version (e.g., `v1.2.3`).

### Consequences of Manual Editing

Manual editing bypasses this integrated workflow and causes severe issues:
- **Version Drift**: The version declared in `package.json` diverges from Git tags.
- **CI/CD Pipeline Breakage**: Modern release pipelines, automated changelogs, container registries, and package registries (NPM, GitHub Packages) rely on Git tags matching the manifest version.
- **Broken Release Tracking**: Git history loses the explicit release boundary, complicating rollbacks, `git bisect`, and semantic comparisons.

---

## Pre-Bump Checklist

Before running `npm version`, ensure the following prerequisites are met:

1. **Clean Working Tree**:
   - The Git working directory MUST be clean:
     ```bash
     git status --porcelain
     ```
   - If uncommitted changes exist, commit or stash them first. Never bypass `npm version`'s clean working tree check with `--force`.
2. **Verification & Tests Pass**:
   - All tests, typechecks, and linters must pass before cutting a version.
3. **Correct Branch**:
   - Confirm you are on the designated release branch (e.g., `main`, `master`, or `release/*`).

---

## Versioning Commands & SemVer Selection

Choose the appropriate SemVer increment based on the nature of changes:

| Command | SemVer Level | When to Use | Example (`1.0.0` -> ) |
| :--- | :--- | :--- | :--- |
| `npm version patch` | **PATCH** | Backward-compatible bug fixes or minor patches | `1.0.1` |
| `npm version minor` | **MINOR** | Backward-compatible new features or enhancements | `1.1.0` |
| `npm version major` | **MAJOR** | Breaking changes, incompatible API modifications | `2.0.0` |

### Pre-release Versioning

For alpha, beta, or release candidate iterations:

```bash
# Start pre-releases
npm version prepatch --preid=alpha   # 1.0.0 -> 1.0.1-alpha.0
npm version preminor --preid=beta    # 1.0.0 -> 1.1.0-beta.0
npm version premajor --preid=rc      # 1.0.0 -> 2.0.0-rc.0

# Increment existing pre-release
npm version prerelease               # 1.0.1-alpha.0 -> 1.0.1-alpha.1
```

### Custom Commit Message

To provide a descriptive commit message alongside the version bump:

```bash
npm version minor -m "Release %s: Add authentication module"
```
*(The `%s` placeholder is replaced with the newly generated version number).*

### Specific Target Version

To bump to an exact explicit version while retaining Git tag creation:

```bash
npm version 2.4.0
```

---

## NPM Lifecycle Hooks Integration

Take advantage of NPM's built-in version lifecycle hooks in `package.json` scripts:

```json
{
  "scripts": {
    "preversion": "npm test && npm run lint",
    "version": "npm run build && git add dist/",
    "postversion": "git push && git push --tags"
  }
}
```

- **`preversion`**: Runs before the version is bumped. Use to enforce testing, linting, or changelog verification. If it fails, the version bump is aborted.
- **`version`**: Runs after `package.json` is modified and staged, but **before** the Git commit and tag are created. Perfect for generating build artifacts, syncing changelogs, and staging them (`git add CHANGELOG.md`).
- **`postversion`**: Runs after the Git commit and tag have been created. Ideal for pushing commits and tags to the remote repository.

---

## Synchronizing with Remote

The `npm version` command creates the commit and tag **locally**. Always remember to push both to the remote repository:

```bash
git push && git push --tags
# Or using --follow-tags (pushes annotated tags on the current branch):
git push --follow-tags
```

---

## Drift Detection & Remediation (Handling Manual Edits)

When inspecting a project, preparing a release, or reviewing changes, the agent must check whether the current `"version"` in `package.json` matches the latest Git version tag.

### Detecting Drift

Run the following checks to identify if a manual version edit occurred:

1. **Uncommitted Version Edit**:
   - Check if `package.json` contains unstaged or staged modifications to `"version"`:
     ```bash
     git diff package.json
     ```
2. **Missing or Mismatched Git Tag**:
   - Extract the current version from `package.json` (e.g., `X.Y.Z`).
   - Check if the matching Git tag exists and points to the appropriate commit:
     ```bash
     git tag -l "vX.Y.Z"
     ```
   - If the tag does not exist or points to an older, unrelated commit, the version has drifted out of sync.

---

### Remediation Workflows

If manual modification or tag desynchronization is detected, **alert the user immediately** and present the following two resolution options:

#### Option 1: Revert Manual Edit and Use `npm version` (Recommended)

This is the cleanest approach as it ensures standard NPM commit and tag generation.

- **If the manual edit is uncommitted**:
  ```bash
  # 1. Discard manual changes in package manifests
  git checkout HEAD -- package.json package-lock.json

  # 2. Run the proper npm version command
  npm version <patch|minor|major|X.Y.Z>
  ```

- **If the manual edit was committed locally (HEAD)**:
  ```bash
  # 1. Undo the commit while keeping working files
  git reset --soft HEAD~1

  # 2. Discard the manual version edits
  git checkout HEAD -- package.json package-lock.json

  # 3. Apply the version bump through npm version
  npm version <patch|minor|major|X.Y.Z>
  ```

#### Option 2: Retroactively Synchronize Git Tag with Manual Version

Use this option if the manual change has already been pushed or must be preserved:

1. **Ensure lockfile is in sync**:
   ```bash
   npm install --package-lock-only
   ```
2. **Commit manifest changes (if not yet committed)**:
   ```bash
   git add package.json package-lock.json
   git commit -m "vX.Y.Z"
   ```
3. **Create the missing annotated Git tag**:
   ```bash
   git tag -a "vX.Y.Z" -m "vX.Y.Z"
   ```
4. **Push commit and tag to remote**:
   ```bash
   git push && git push origin "vX.Y.Z"
   ```

---

## Agent Guardrails & Interception

When acting as an AI assistant:

1. **Intercept Manual Edits**: If requested to "change the version to X", "bump version in package.json", or "prepare release", **do NOT use file modification tools** (`edit_file` or `write_file`) on `package.json` to alter `"version"`.
2. **Execute Terminal Command**: Run `npm version <major|minor|patch|version>` via the shell execution tool.
3. **Detect & Alert on Drift**: Whenever inspecting a repository before a release or build, verify if `package.json` version matches Git tags. If a manual edit or missing tag is detected:
   - Alert the user proactively.
   - Explain that `package.json` and Git tags are out of sync.
   - Offer the two remediation paths (revert + `npm version` or create retroactive tag).
4. **Notify User**: Clearly explain that `npm version` was used to ensure synchronization between `package.json` and Git version tags.
