---
name: create-branch
description:
  Create git branches following naming conventions. Use when creating new
  feature branches, bug fix branches, or any branch for development work.
  Defines the branch naming standards used across commit and PR skills.
---

# Create Branch

Create git branches following consistent naming conventions.

## Branch Naming Convention

Branch names should follow the pattern: `<type>/<short-description>`

The type should match the primary type of work being done (same as commit
types).

### Branch Types

| Type    | When to Use                           | Example                      |
| ------- | ------------------------------------- | ---------------------------- |
| `feat`  | New feature or functionality          | `feat/add-user-auth`         |
| `fix`   | Bug fix                               | `fix/null-pointer-error`     |
| `ref`   | Refactoring (no behavior change)      | `ref/extract-validation`     |
| `perf`  | Performance improvement               | `perf/optimize-queries`      |
| `docs`  | Documentation changes                 | `docs/update-readme`         |
| `test`  | Adding or correcting tests            | `test/add-integration-tests` |
| `build` | Build system or dependencies          | `build/upgrade-webpack`      |
| `ci`    | CI/CD configuration                   | `ci/add-github-actions`      |
| `chore` | Maintenance, cleanup, or housekeeping | `chore/update-deps`          |
| `style` | Code formatting (no logic change)     | `style/format-components`    |

### Description Guidelines

The description should be:

- **Short**: 2-4 words maximum
- **Descriptive**: Clearly indicates what the branch is for
- **Lowercase**: Use lowercase with hyphens (kebab-case)
- **No special characters**: Only letters, numbers, and hyphens

### Good Branch Names

```bash
feat/add-user-auth              # Clear, concise, descriptive
fix/null-pointer-dashboard      # Specific about what's being fixed
ref/extract-validation-logic    # Clear refactoring goal
test/add-api-tests              # Clear testing scope
```

### Bad Branch Names

```bash
feature/add-authentication-system-for-users    # Too long
fix-bug                                         # Too vague
feat/AddUserAuth                                # Wrong case (use lowercase)
john/my-work                                    # Personal prefix (not descriptive)
feat_add_auth                                   # Wrong separator (use hyphens)
```

## Creating a Branch

### Check Current Branch First

Before creating a new branch, verify where you are:

```bash
# Check current branch
git branch --show-current

# See if you have uncommitted changes
git status
```

**If you have uncommitted changes:**

- Commit them first, OR
- Stash them: `git stash`

### Create and Switch to New Branch

```bash
# Create and switch to new branch from current location
git checkout -b <type>/<short-description>
```

### Create from Specific Base Branch

If you need to branch from a specific base (like `main` or `develop`):

```bash
# Make sure base branch is up to date
git checkout main
git pull

# Create new branch from main
git checkout -b feat/add-user-auth
```

## Examples

### Creating a Feature Branch

```bash
# Starting from main
git checkout main
git pull
git checkout -b feat/add-oauth

# Verify you're on the new branch
git branch --show-current  # Should output: feat/add-oauth
```

### Creating a Fix Branch

```bash
# Starting from develop
git checkout develop
git pull
git checkout -b fix/session-timeout

# Verify
git branch --show-current  # Should output: fix/session-timeout
```

### Creating a Refactor Branch

```bash
# From current branch (already has related work)
git checkout -b ref/extract-utils

# Verify
git branch --show-current  # Should output: ref/extract-utils
```

## Branch Workflow

### 1. Check Base Branch

```bash
# If you need to branch from main/master
git checkout main
git pull
```

### 2. Create Branch

```bash
# Create with appropriate type and description
git checkout -b feat/add-feature-name
```

### 3. Verify Branch

```bash
# Confirm you're on the new branch
git branch --show-current

# Should show: feat/add-feature-name
```

### 4. Push Branch (When Ready)

```bash
# Push and set upstream tracking
git push -u origin feat/add-feature-name
```

## Common Mistakes to Avoid

| Mistake         | Bad Example          | Good Example       |
| --------------- | -------------------- | ------------------ |
| Too long        | feat/add-user-system | feat/add-user-auth |
| Too vague       | fix/bug              | fix/null-pointer   |
| Wrong case      | Feat/AddAuth         | feat/add-auth      |
| Personal prefix | john/feature         | feat/feature-name  |
| Wrong separator | feat_add_auth        | feat/add-auth      |
| No type         | add-authentication   | feat/add-auth      |

## Referencing Branch Names

Other skills reference these conventions:

- **commit skill**: Uses branch naming to ensure commits are on feature
  branches, not main/master
- **create-pr skill**: Uses branch naming for PR titles and validation

## Checklist Before Creating Branch

- [ ] Verified current branch and location
- [ ] Base branch is up to date (if branching from main/develop)
- [ ] No uncommitted changes (or stashed)
- [ ] Branch name follows `<type>/<short-description>` format
- [ ] Type matches the work being done
- [ ] Description is 2-4 words, lowercase, hyphen-separated
- [ ] Branch name is descriptive and clear

## Tool Usage

- **Use `git checkout -b <branch-name>`** to create and switch to new branch
- **Use `git branch --show-current`** to verify current branch
- **Use `git status`** to check for uncommitted changes
- **Use `git push -u origin <branch-name>`** to push new branch and set tracking
- **DO NOT use `git checkout -b` with `-i` or interactive flags** (not
  supported)
