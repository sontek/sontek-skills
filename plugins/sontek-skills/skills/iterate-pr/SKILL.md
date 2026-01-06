---
name: iterate-pr
description:
  Iterate on a PR until CI passes and feedback is addressed. Use when fixing CI
  failures, addressing review comments, or continuously improving a PR until all
  checks are green. Automates the feedback-fix-push cycle.
---

# Iterate on PR Until CI Passes

Continuously iterate on the current branch until all CI checks pass and review feedback is addressed.

**Requires**: GitHub CLI (`gh`) authenticated and available.

## Core Principles

- **Be systematic**: Check CI, gather feedback, fix issues, repeat
- **Be targeted**: Only fix actual issues, don't over-engineer
- **Be efficient**: Wait for bots before addressing feedback
- **Be persistent**: Keep iterating until all checks pass
- **Communicate**: Comment on PR when pushing significant updates

## Change Discipline

- Write absolute minimum code required
- No sweeping changes
- No unrelated edits
- Stay focused on the specific task
- Don't break existing functionality without asking

## Investigation Approach

When CI fails or issues arise:

1. List 5-7 possible causes from the logs and context
2. Gather evidence for each (check related code, tests, similar issues)
3. Narrow to 1-2 most likely causes based on evidence
4. Validate assumptions (run tests locally, add logging, inspect code)
5. Only then implement the fix

This prevents guess-and-check cycles and wasted iterations.

## Process Overview

```
1. Identify PR
2. Check CI status
3. Wait for bots if still pending
4. Gather review feedback
5. Investigate failures
6. Validate feedback
7. Make targeted fixes
8. Commit and push
9. Wait for CI
10. Repeat if needed
```

## Step-by-Step Process

### Step 1: Identify the PR

```bash
gh pr view --json number,url,headRefName,baseRefName,title
```

**Verify:**
- PR exists for current branch
- You're on the correct branch
- PR is not closed or merged

**If no PR exists:** Stop and inform the user to create one first.

### Step 2: Check CI Status

Always check CI status before doing anything else:

```bash
gh pr checks --json name,state,bucket,link,workflow
```

**Understanding bucket values:**
- `pass` - Check passed
- `fail` - Check failed
- `pending` - Still running
- `skipping` - Skipped (not required)
- `cancel` - Cancelled

**If all checks pass:** Move to Step 3 to check for review feedback.

**If checks are pending:** Wait for them to complete (see Step 9).

**If checks failed:** Continue to Step 4 to investigate.

### Step 3: Wait for Bot Checks

Before addressing feedback, wait for these bots if they're still `pending`:

- `codecov` - Code coverage reporting
- `cursor` / `bugbot` / `seer` - Code analysis bots
- Linters (eslint, pylint, etc.)
- Security scanners

**Why wait?** These bots often post additional review comments after their checks complete. Waiting avoids duplicate work.

**How to wait:**
```bash
gh pr checks --watch --interval 30
```

### Step 4: Gather Review Feedback

Once bot checks complete, gather all feedback:

**Review comments and status:**
```bash
gh pr view --json reviews,comments,reviewDecision
```

**Inline code review comments:**
```bash
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments
```

**PR conversation comments (includes bot feedback):**
```bash
gh api repos/{owner}/{repo}/issues/{pr_number}/comments
```

**Organize feedback:**
- Group by type: CI failures, human reviews, bot suggestions
- Prioritize: CI failures first, then blocking reviews, then suggestions
- Note which issues are related

### Step 5: Investigate CI Failures

For each CI failure, get the actual logs:

```bash
# List recent runs for this branch
gh run list --branch $(git branch --show-current) --limit 5 --json databaseId,name,status,conclusion

# View logs for a specific run
gh run view <run-id> --log-failed

# Or see all job steps
gh run view <run-id> --verbose
```

**Important:** Always read the actual error logs. Don't assume what failed based on the check name alone.

**Look for:**
- Exact error messages and stack traces
- Which test or step failed
- Line numbers of failures
- Whether it's a flaky test (passes sometimes)

### Step 6: Validate Feedback

Before fixing, verify each issue:

1. Read the relevant code using Read tool
2. Confirm the issue is real (not false positive, already fixed, or out of scope)
3. Determine if it must be fixed in this PR
4. Skip invalid or out-of-scope feedback

### Step 7: Make Targeted Fixes

Fix only the specific issue - no refactoring, no extra features, minimal changes.

**Approach by type:**
- **Tests**: Read test + code, fix logic or update test expectation
- **Types**: Fix properly, don't cast to `any`
- **Linter**: Use auto-fix (`npm run lint -- --fix`) or edit using Edit tool
- **Security**: Fix properly and add tests
- **Review**: Address concern or explain why not (ask if unclear)

### Step 8: Commit and Push

Follow commit message conventions from the commit skill:

```bash
git add <files>
git commit -m "fix(scope): Brief description of what was fixed"
git push
```

**Commit message guidelines:**
- Use `fix:` prefix for bug fixes
- Be specific about what was fixed
- Reference issue if applicable
- Keep under 72 characters

**Examples:**
```bash
git commit -m "fix(tests): Update user factory for new schema"
git commit -m "fix(api): Add null check in profile endpoint"
git commit -m "fix(lint): Remove unused imports"
```

**Add PR comment for significant changes:**
```bash
gh pr comment --body "Fixed test failures by updating user factory for new schema changes. All tests now pass locally."
```

### Step 9: Wait for CI

Use the watch functionality to wait for checks:

```bash
gh pr checks --watch --interval 30
```

**This command:**
- Polls checks every 30 seconds
- Exits with code 0 if all pass
- Exits with code 1 if any fail
- Shows progress as checks complete

**Alternative - manual polling:**
```bash
# See only non-passing checks
gh pr checks --json name,state,bucket | jq '.[] | select(.bucket != "pass")'

# Focus on required checks only
gh pr checks --required
```

**While waiting:**
- Don't make additional changes
- Don't push new commits
- Wait for all checks to complete

### Step 10: Repeat If Needed

**Return to Step 2 if:**
- Any CI checks failed
- New review feedback appeared
- Requested changes need addressing

**Continue iterating until:**
- All CI checks are green (`bucket: pass`)
- All blocking review feedback is addressed
- No new issues appear

## Exit Conditions

### Success - Stop Iterating

✓ All CI checks pass
✓ All blocking review feedback addressed
✓ No new failures introduced
✓ PR is ready for merge

**Report to user:**
```
All CI checks passing. PR is ready for review/merge.

Final status:
- ✓ Tests passing
- ✓ Lint checks passing
- ✓ Coverage meets threshold
- ✓ All review feedback addressed
```

### Ask for Help - Stop and Request Input

Stop and ask user when:

- **Same failure after 3 attempts**: Likely flaky test or deeper issue
- **Unclear review feedback**: Requires clarification or decision
- **Conflicting feedback**: Multiple reviewers want different things
- **Out of scope**: Request is beyond PR's intended changes
- **Infrastructure issue**: CI failure unrelated to code changes

**Example:**
```
CI failure persists after 3 attempts fixing test_user_login.

The test fails intermittently with "Database connection timeout."
This appears to be a flaky test or infrastructure issue, not a code problem.

Should I:
1. Skip/disable this test?
2. Investigate further?
3. Wait for infrastructure team?
```

### Stop Immediately - Cannot Continue

- **No PR exists**: User needs to create PR first
- **Branch out of sync**: Needs rebase or merge from base branch
- **PR is closed/merged**: Cannot push to closed PR
- **No write access**: Permissions issue

## Common CI Failures and Fixes

### Test Failures

**Symptom:**
```
FAILED tests/test_user.py::test_create_user - AssertionError
Expected: "john@example.com"
Got: None
```

**Fix:**
- Read the test code
- Understand what behavior changed
- Fix the code or update test expectation
- Run tests locally to verify

### Type Errors

**Symptom:**
```
src/api/user.py:45: error: Argument 1 to "get_profile" has incompatible type "str"; expected "int"
```

**Fix:**
- Check function signature
- Convert types properly (don't cast to `any`)
- Add proper type annotations

### Linter Errors

**Symptom:**
```
src/utils.py:23:1: E302 expected 2 blank lines, found 1
src/api.py:45:80: E501 line too long (85 > 79 characters)
```

**Fix:**
```bash
# Auto-fix if possible
npm run lint -- --fix
ruff check --fix .
black .

# Or edit the files directly using Edit tool
```

## Tool Usage

- **Use `gh pr view`** to get PR details and reviews
- **Use `gh pr checks`** to see CI status
- **Use `gh run view`** to read CI logs
- **Use `gh pr comment`** to add comments when pushing updates
- **Use Read tool** to examine files mentioned in feedback
- **Use Grep tool** to search for patterns causing failures
- **Use TodoWrite tool** to track which issues are fixed/pending

## Using TodoWrite for Iteration

Track your progress through the iteration:

```
[pending] Fix test_user_login failure
[in_progress] Address review feedback on auth.py
[pending] Fix type errors in api.py
[pending] Update docstrings per review
```

Mark as completed as you fix each issue. This helps track progress through multiple iterations.

## Common Mistakes to Avoid

| Mistake                 | Avoid                           | Do Instead                       |
| ----------------------- | ------------------------------- | -------------------------------- |
| Over-fixing             | Fix unrelated code              | Only fix the specific issue      |
| Ignoring feedback       | Dismiss reviewer comments       | Address or discuss each point    |
| Unverified pushes       | Push without local testing      | Verify locally first             |
| Bundling changes        | Multiple unrelated fixes in one | Separate commits for each fix    |
| Giving up early         | Stop after first failure        | Investigate logs thoroughly      |

## Communication

When pushing significant changes, add a concise PR comment:

```bash
gh pr comment --body "Fixed null check per @reviewer and added test coverage. All checks passing locally."
```

Keep it brief (1-2 sentences or 3-5 bullets). Don't repeat commit messages or over-explain.

## Tips

**Focus on required checks:**
```bash
gh pr checks --required
```

**See external check links:**
```bash
gh pr checks --json name,link
```

**Watch specific workflow:**
```bash
gh run watch <run-id>
```

**Re-run failed checks (if permissions allow):**
```bash
gh run rerun <run-id> --failed
```

**Get check details programmatically:**
```bash
# View logs for failed checks
gh run view <run-id> --log-failed

# Get structured check data
gh pr checks --json name,state,conclusion
```

## Iteration Example

**Iteration 1:**
- CI Status: 2 checks failing (tests, lint)
- Read logs: test_user_login fails, 3 lint errors
- Fix test by updating user factory
- Fix lint with auto-formatter
- Commit: "fix: Update user factory and fix lint errors"
- Push and wait for CI

**Iteration 2:**
- CI Status: All checks pass
- New review feedback: "Add null check in line 45"
- Read code, verify issue is real
- Add null check with test
- Commit: "fix(api): Add null check in profile endpoint"
- Comment: "Added null check per @reviewer, includes test coverage"
- Push and wait for CI

**Iteration 3:**
- CI Status: All checks pass
- No new feedback
- Success! Report completion to user

## Summary

The iterate-pr skill automates the feedback loop:
1. Check what's broken
2. Fix it
3. Push and wait
4. Repeat until green

Be systematic, targeted, and persistent. Most PRs need 1-3 iterations to pass all checks.
