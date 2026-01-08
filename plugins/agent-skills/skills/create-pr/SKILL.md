---
name: create-pr
description:
  Create pull requests following good conventions. Use when opening PRs, writing
  PR descriptions, or preparing changes for review. Emphasizes concise, helpful
  descriptions that respect reviewers' time.
---

# Create Pull Request

Create pull requests that are easy to review and understand.

**Requires**: GitHub CLI (`gh`) authenticated and available.

## Core Principles

- **Be concise**: Respect reviewers' time - no fluff or unnecessary words
- **Be helpful**: Provide context that isn't obvious from the code
- **Be clear**: Explain what and why, not how (code shows how)
- **Be focused**: One PR per feature/fix - don't bundle unrelated changes

## Change Discipline

- Write absolute minimum code required
- No sweeping changes
- No unrelated edits
- Stay focused on the specific task
- Don't break existing functionality without asking

## Process

### Step 1: Check for PR Template

Before creating the PR, check if the repository has a PR template:

```bash
# Check for PR template files
ls -la .github/pull_request_template.md
ls -la .github/PULL_REQUEST_TEMPLATE.md
ls -la .github/PULL_REQUEST_TEMPLATE/*.md
ls -la docs/pull_request_template.md
```

**If a template exists:**

- Use the template structure
- Fill in all required sections
- Remove any sections marked as optional that don't apply
- Follow the template's formatting

**If no template exists:**

- Follow the structure outlined in this document
- Focus on being concise and helpful

### Step 2: Verify Branch State

```bash
# Check current branch and status
git status

# See what commits will be in the PR
git log main..HEAD --oneline

# Verify branch is pushed
git push -u origin HEAD
```

**Ensure:**

- All changes are committed
- Branch is pushed to remote
- Branch name follows conventions: `<type>/<short-description>` (e.g., `feat/add-oauth`, `fix/null-pointer`). See **create-branch** skill for details.
- No unintended changes are included
- Branch is up to date with base branch if needed

### Step 3: Review Your Own Changes

Review the PR diff before creating it:

```bash
# See all commits that will be in the PR
git log main..HEAD

# See the full diff
git diff main...HEAD
```

**Self-review checklist:**

- [ ] All commits are related to the same logical change
- [ ] No debug code, console.logs, or commented code
- [ ] No secrets or credentials
- [ ] Code follows project conventions
- [ ] Tests are included (if applicable)
- [ ] Documentation is updated (if needed)

### Step 4: Write the PR Description

**Writing principles:**

- **Concise and direct**: Get to the point quickly
- **Informative**: Provide context reviewers need
- **Scannable**: Use short paragraphs and bullet points
- **No redundancy**: Don't repeat what's obvious from the diff

**Description structure:**

```markdown
<Brief 1-2 sentence summary of what this PR does>

<Why these changes are being made - the motivation and context>

<Any important details reviewers should know>

<Issue references>
```

**Do include:**

- Clear explanation of what and why
- Links to relevant issues or tickets
- Context that isn't obvious from the code
- Areas that need careful review
- Alternative approaches considered (if relevant)
- Breaking changes or migration steps
- Performance implications (if any)

**Do NOT include:**

- Verbose narratives or unnecessary backstory
- "Test plan" sections with checkbox lists
- Redundant summaries of the diff
- Obvious information already in the code
- Bullet points that just list file names changed
- Phrases like "In this PR, I have..." or "This PR is about..."

### Step 5: Create the PR

```bash
# Create PR with title and body
gh pr create --title "<type>(<scope>): <description>" --body "$(cat <<'EOF'
<description body here>
EOF
)"
```

**Title format** follows commit conventions:

- `feat(scope): Add new feature`
- `fix(scope): Fix the bug`
- `ref: Refactor something`
- Maximum 72 characters
- Imperative mood, no period at end

### Step 6: Add Reviewers and Labels

```bash
# Request review from specific people
gh pr edit --add-reviewer username1,username2

# Or request from a team
gh pr edit --add-reviewer @team-name

# Add labels if applicable
gh pr edit --add-label bug,needs-review
```

**Reviewer guidelines:**

- Limit to 1-3 reviewers for clear ownership
- Tag specific people if certain expertise is needed
- Use draft PRs for early feedback before formal review

## PR Description Examples

### Feature PR

```markdown
Add Slack thread replies for alert notifications

When an alert is updated or resolved, post a reply to the original Slack thread
instead of creating a new message. This keeps related notifications grouped and
reduces channel noise.

Previously considered editing the original message, but threading better
preserves event timeline and works beyond Slack's edit window.

Refs ENG-1234
```

### Bug Fix PR

```markdown
Handle null response in user API endpoint

The user endpoint returns null for soft-deleted accounts, causing dashboard
crashes when accessing user.profile. Add null check and return proper 404
response instead.

Fixes ENG-5678
```

### Refactor PR

```markdown
Extract validation logic to shared module

Move duplicate validation from alerts, issues, and projects endpoints into
shared validator class. No behavior change.

This prepares for adding new validation rules (ENG-9999) without duplicating
logic across endpoints.

Refs ENG-9999
```

## Description Length Guidelines

**Short PR (1-10 files, single commit):**

- 2-4 sentences usually sufficient
- Focus on the "why"

**Medium PR (10-50 files, multiple related commits):**

- 1 paragraph + bullet points for key areas
- 50-100 words typical

**Large PR (50+ files or architectural change):**

- 2-3 paragraphs + bullet points
- 100-200 words maximum
- Consider if it should be split into smaller PRs

**Rule of thumb:** If your description is longer than 200 words, it's probably
too verbose or the PR is too large.

## Common Mistakes to Avoid

| Mistake         | Bad Example                | Good Example                           |
| --------------- | -------------------------- | -------------------------------------- |
| Too verbose     | Long narrative, file lists | 2-4 concise sentences with context     |
| Too vague       | "Update user code"         | "Add profile editing and fix null bug" |
| Redundant info  | Listing changed files      | Context not obvious from diff          |
| Missing context | "Refactored to be better"  | Explain why and what changes           |

## PR Templates

When a repository has a PR template, respect its structure:

### Handling Optional Sections

```markdown
# If template has optional sections you don't need:

## Description

Add caching for search results

## Breaking Changes

None

## Testing

~~- [ ] Manual testing performed~~ # Remove if not applicable ~~- [ ] Added unit
tests~~ # Remove if not applicable
```

Better: Just omit optional sections entirely if they don't apply.

### Adapting to Template Style

- If template asks for bullet points, use them
- If template asks for specific sections, include them
- If template is overly verbose, be concise within its structure
- Don't fight the template, but don't add fluff to fill it

## Guidelines

### PR Size

- **Small PRs get reviewed faster**: Aim for < 400 lines changed
- **Split large changes**: Multiple small PRs better than one huge PR
- **One logical change per PR**: Don't bundle unrelated changes

### Draft PRs

Use draft PRs when:

- You want early feedback on approach
- Work is incomplete but you want visibility
- You need to verify CI passes before requesting review

```bash
gh pr create --draft --title "WIP: Feature name"
```

Convert to ready when complete:

```bash
gh pr ready
```

### PR Updates

When pushing new commits after review:

- Add a comment summarizing what changed
- Don't force-push unless asked to squash
- Keep the PR description updated if scope changes

```bash
# Add comment about updates
gh pr comment --body "Updated based on review feedback:
- Changed validation approach per @reviewer
- Added test case for edge case
- Fixed typo in documentation"
```

## Issue References

Reference issues in the PR body:

| Syntax                | Effect                       |
| --------------------- | ---------------------------- |
| `Fixes #1234`         | Closes GitHub issue on merge |
| `Closes #1234`        | Same as Fixes                |
| `Fixes ENG-1234`      | Closes Jira ticket           |
| `Refs GH-1234`        | Links without closing        |
| `Refs LINEAR-ABC-123` | Links Linear issue           |

**Multiple issues:**

```markdown
Fixes ENG-1234 Refs ENG-5678, ENG-9012
```

## Checklist Before Creating PR

- [ ] Branch is pushed to remote
- [ ] All commits are related to same logical change
- [ ] No debug code, secrets, or commented code
- [ ] Self-reviewed the diff
- [ ] Tests pass locally (if applicable)
- [ ] Checked for PR template in repository
- [ ] PR title follows commit conventions
- [ ] Description is concise but helpful
- [ ] Issue references are included
- [ ] Reviewers are assigned (if known)

## Tool Usage

- **Use `gh pr create`** to create PRs from command line
- **Use `gh pr view`** to view PR details
- **Use `gh pr edit`** to modify title, body, reviewers, labels
- **Use `gh pr comment`** to add comments when pushing updates
- **Use `gh pr ready`** to convert draft to ready for review
- **Use `git diff main...HEAD`** to see full PR diff before creating
