---
name: commit
description:
  Create commit messages following good conventions. Use when committing code
  changes, writing commit messages, or formatting git history. Follows
  conventional commits and best practices.
---

# Commit Messages

Follow these conventions when creating commits.

## Change Discipline

- Write absolute minimum code required
- No sweeping changes
- No unrelated edits
- Stay focused on the specific task
- Don't break existing functionality without asking

## Branch Management

Before committing, always verify you're on the correct branch.

### Check Current Branch

```bash
# Check current branch
git branch --show-current
```

### Create Feature Branch

**CRITICAL: If on `main` or `master`, you MUST create a feature branch first before committing.**

Branch names follow the pattern: `<type>/<short-description>`
- Type matches commit type (feat, fix, ref, test, etc.)
- Description is 2-4 words, lowercase, hyphen-separated
- Examples: `feat/add-user-auth`, `fix/null-pointer`, `ref/extract-validation`

```bash
# Create and switch to new branch
git checkout -b <type>/<short-description>
```

See the **create-branch** skill for complete branch naming conventions.

**Example:**

```bash
# Bad - committing directly to main
git branch --show-current  # main
git commit -m "feat: Add user authentication"  # Don't do this!

# Good - create feature branch first
git branch --show-current  # main
git checkout -b feat/add-user-auth
git commit -m "feat(auth): Add user authentication"
```

## Commit Workflow

### 1. Review Changes

Before committing, always review what you're about to commit:

```bash
# See what files have changed
git status

# Review all changes
git diff

# Review staged changes only
git diff --cached

# Review recent commit history for style consistency
git log --oneline -10
```

**Verify:**

- All changes are related to a single logical change
- No unintended changes are included
- No debug code, console.logs, or temporary changes
- No secrets, credentials, or sensitive data
- No extraneous comments that humans that describe obvious things

### 2. Stage Relevant Files

```bash
# Stage specific files
git add path/to/file1 path/to/file2

# Stage all changes (use cautiously)
git add .

# Stage all changes in a directory
git add src/components/
```

### 3. Write the Commit Message

Follow the format below, ensuring the message clearly explains what and why.

### 4. Verify the Commit

After committing:

```bash
# Verify the commit looks correct
git show

# Check commit history
git log -1
```

## Message Format

```
<type>(<scope>): <subject>
# ################### 50 chars is here: #

<body>
# Wrap at 72 chars ################################## which is here: #

<footer>
```

### Format Rules

- **Subject line**: Maximum 50 characters (hard limit: 72 characters)
- **Body lines**: Wrap at 72 characters
- **Blank line**: Required between subject and body
- **Blank line**: Required before footer

**Note:** While conventional commits allow up to 100 characters, limiting to
50-72 makes messages more readable in terminal, GitHub UI, and git log outputs.

## Commit Types

Choose the type that best describes your change:

| Type      | When to Use                                                 |
| --------- | ----------------------------------------------------------- |
| `feat`    | New feature or functionality for users                      |
| `fix`     | Bug fix that corrects incorrect behavior                    |
| `ref`     | Code refactoring with no behavior change                    |
| `perf`    | Performance improvement (faster, less memory, etc.)         |
| `docs`    | Documentation changes only (README, comments, etc.)         |
| `test`    | Adding or correcting tests                                  |
| `build`   | Build system, dependencies, or tooling (npm, webpack, etc.) |
| `ci`      | CI/CD configuration (GitHub Actions, Jenkins, etc.)         |
| `chore`   | Maintenance tasks, cleanup, or housekeeping                 |
| `style`   | Code formatting, white space, missing semicolons (no logic) |
| `revert`  | Reverts a previous commit                                   |
| `meta`    | Repository metadata (package.json version, etc.)            |
| `license` | License changes                                             |

### Type Selection Guide

**Use `feat` when:**

- Adding a new user-facing feature
- Adding a new API endpoint
- Adding a new command or option
- Introducing new functionality

**Use `fix` when:**

- Correcting a bug or error
- Fixing incorrect behavior
- Resolving a regression
- Addressing a reported issue

**Use `ref` when:**

- Extracting a function or class
- Renaming for clarity
- Restructuring code organization
- Simplifying complex logic
- No observable behavior changes

**Use `perf` when:**

- Optimizing algorithms
- Reducing memory usage
- Improving query performance
- Reducing bundle size

## Scope

The scope specifies which part of the codebase is affected. Common scopes:

- **Module names**: `api`, `auth`, `dashboard`, `cli`
- **Component names**: `UserProfile`, `Header`, `Button`
- **Feature areas**: `billing`, `notifications`, `search`
- **File types**: `models`, `views`, `controllers`

**Omit scope** when the change affects multiple areas or the entire codebase.

## Subject Line

The subject line is a concise summary of the change.

### Rules

1. **Use imperative mood**: "Add feature" not "Added feature" or "Adds feature"
2. **Start with capital letter**: "Fix bug" not "fix bug"
3. **No period at end**: "Update API" not "Update API."
4. **Maximum 50 characters** (hard limit: 72)
5. **Be specific**: "Fix null pointer in user profile" not "Fix bug"

### Examples

```
# Good - specific, imperative, under 50 chars
feat(auth): Add OAuth2 support for Google login
fix(api): Handle null response in user endpoint
ref(database): Extract query builder to separate module

# Bad - vague, wrong tense, or too long
fix: bug fix                           # Too vague
feat: added new feature                # Past tense
feat(api): endpoint for user profile management and settings  # Too long
```

## Body

The body provides detailed context about the change.

### Guidelines

- Explain what and why, not how (code shows how)
- Wrap at 72 characters
- Use imperative mood
- Be concise and direct - no fluff
- Always include body for: features, non-obvious fixes, breaking changes,
  performance improvements
- Body optional for: docs, typos, simple tests

### Body Examples

#### Good Body - Feature

```
feat(alerts): Add Slack thread replies for alert updates

When an alert is updated or resolved, post a reply to the original
Slack thread instead of creating a new message. This keeps related
notifications grouped together and reduces noise in the channel.

Users can still see the full alert history in a single thread.
```

#### Good Body - Bug Fix

```
fix(api): Handle null response in user endpoint

The user API returns null for deleted accounts, which caused the
dashboard to crash when accessing user properties. Add null check
before accessing user.profile to prevent the error.

The dashboard now shows a "User not found" message instead of
crashing.

Fixes ENG-5678
```

### Body Wrapping Guide

```
# Wrap at 72 chars ################################## which is here: #
The user API returns null for deleted accounts, which caused the
dashboard to crash when accessing user properties.
```

## Footer

The footer contains metadata about the commit.

### Issue References

Link to issues or tickets using these formats:

```
Fixes GH-1234        # Closes GitHub issue #1234
Fixes #1234          # Closes issue in current repo
Closes #1234         # Same as Fixes
Resolves #1234       # Same as Fixes
Fixes ENG-1234       # Closes Jira ticket ENG-1234
Refs LINEAR-ABC-123  # References Linear issue (no close)
Refs #1234           # References issue (no close)
```

**When to use:**

- `Fixes`/`Closes`/`Resolves`: Automatically closes the issue when merged
- `Refs`: Links to issue without closing (for related work)

### Co-authored-by

Credit all contributors to the commit:

```
Co-authored-by: Jane Smith <jane@example.com>
Co-authored-by: John Doe <john@users.noreply.github.com>
```

**When to include:**

- Pair programming sessions
- Code from code review suggestions
- Significant contributions from others
- Cherry-picked commits from other authors

### Breaking Changes

For breaking changes, use one of these formats:

```
BREAKING CHANGE: Description of what broke and migration path
```

Or add `!` after the type/scope:

```
feat(api)!: Remove deprecated v1 endpoints
```

### Complete Footer Example

```
Fixes ENG-5678
Refs #1234

Co-authored-by: Jane Smith <jane@example.com>
```

## Complete Examples

### Example 1: Feature with Scope

```
feat(alerts): Add Slack thread replies for alert updates

When an alert is updated or resolved, post a reply to the original
Slack thread instead of creating a new message. This keeps related
notifications grouped together and reduces noise in the channel.

Users can still see the full alert history in a single thread.

Refs GH-1234

Co-authored-by: Jane Smith <jane@example.com>
```

### Example 2: Bug Fix

```
fix(auth): Prevent session timeout during active use

Sessions were timing out after 30 minutes even if the user was
actively using the app. Update the session middleware to refresh
the timeout on each authenticated request.

This prevents users from being logged out while working.

Fixes ENG-5678
```

### Example 3: Breaking Change

```
feat(api)!: Remove deprecated v1 endpoints

Remove all v1 API endpoints that were deprecated in version 23.1.
The v2 API has been stable for 6 months and all known clients have
migrated.

BREAKING CHANGE: v1 endpoints no longer available. Clients must use
v2 endpoints. See migration guide at docs/api/v1-to-v2-migration.md

Fixes ENG-9999

Co-authored-by: John Doe <john@example.com>
```

## Commit Principles

### Atomic Commits

Each commit should represent a single logical change:

**Good (atomic):**

- One commit: "feat(auth): Add OAuth2 support"
- Next commit: "test(auth): Add OAuth2 integration tests"
- Next commit: "docs: Update authentication documentation"

**Bad (not atomic):**

- One commit: "Add OAuth2, fix unrelated bug, update docs, refactor utils"

### Principles

- **Atomic**: One logical change per commit (easier to review, revert,
  cherry-pick, debug)
- **Working state**: Code compiles and tests pass after each commit
- **Self-contained**: Each commit makes sense independently with clear message

## Common Mistakes to Avoid

| Mistake        | Bad Example                      | Good Example                        |
| -------------- | -------------------------------- | ----------------------------------- |
| Too vague      | `fix: bug fix`                   | `fix(auth): Prevent double login`   |
| Wrong tense    | `feat: Added feature`            | `feat: Add feature`                 |
| Too long       | `feat: Add endpoint for X, Y, Z` | Use body for details                |
| No context     | `fix: Fix bug` (no body)         | Include body explaining what/why    |
| Too verbose    | Long narrative in body           | Concise facts: what changed and why |
| Not imperative | `fixing`, `fixed`, `adds`        | `fix`, `add`, `update`              |

## Tool Usage

When creating commits:

- **Use `git status`** to see which files changed
- **Use `git diff`** to review all changes before staging
- **Use `git diff --cached`** to review staged changes before committing
- **Use `git log --oneline -10`** to see recent commit message style
- **Use specific file paths** when staging subset of changes:
  `git add path/to/file`
- **Use `git show`** after committing to verify the commit looks correct
- **Use `git commit --amend`** ONLY for the most recent unpushed commit

**Do NOT use interactive commands:**

- ❌ `git add -i` - interactive staging
- ❌ `git add -p` - patch mode
- ❌ `git rebase -i` - interactive rebase
- ❌ Any command with `-i` or `--interactive` flag

These require interactive terminal input which is not supported. Use specific
file paths with `git add` instead.

## Pre-commit Checklist

Before committing, verify:

- [ ] On correct branch - **MUST NOT be `main` or `master`** (create feature branch first: `git checkout -b <type>/<short-description>`)
- [ ] Changes are related to a single logical change (atomic commit)
- [ ] All changes are intentional (no debug code, console.logs)
- [ ] No secrets, credentials, or sensitive data
- [ ] Tests pass (or commit is marked as WIP)
- [ ] Code compiles/builds without errors
- [ ] Commit message follows format (type, subject, body, footer)
- [ ] Subject line is under 50 characters (hard limit: 72)
- [ ] Body is wrapped at 72 characters
- [ ] Commit message explains what and why, not how
- [ ] Issue references are included if applicable
- [ ] Co-authors are credited if applicable

## References

- [Conventional Commits](https://www.conventionalcommits.org/)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
  by Chris Beams
- [Git Commit Best Practices](https://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project)
