---
name: deslop
description:
  Remove AI-generated code slop from a branch. Use when cleaning up AI-generated
  code, removing unnecessary comments, defensive checks, or type casts. Checks
  diff against main and fixes style inconsistencies.
---

# Remove AI Code Slop

Review the diff against main and remove AI-generated patterns that are inconsistent with the codebase style.

## Change Discipline

- Write absolute minimum code required
- No sweeping changes
- No unrelated edits
- Stay focused on the specific task
- Don't break existing functionality without asking

## What is "Slop"?

Code patterns that AI commonly adds but human developers in this codebase don't:

### Always Remove

**Excessive comments:**
- Comments that describe obvious code
- Type/parameter documentation for simple functions
- Comments inconsistent with file's commenting style
- "Helper function to..." style comments

**Defensive programming not used elsewhere:**
- Try/catch blocks in code paths that don't use them
- Null checks where the value is guaranteed non-null
- Type guards for internal functions with trusted callers
- Validation for already-validated data

**Type system workarounds:**
- Casts to `any` to bypass type errors
- `as unknown as Type` chains
- Ignoring type errors with comments

**Import style violations:**
- Inline imports in Python (move to top with other imports)
- Inconsistent import ordering
- Unused imports added "just in case"

**Style inconsistencies:**
- Different naming conventions than the file uses
- Different quote styles (single vs double)
- Different indentation or spacing
- Verbose code where file uses concise patterns

### Sometimes Keep (Check Context)

**Error handling:**
- New error handling might be legitimate improvement
- Check if similar code paths have error handling
- If the codebase has no error handling anywhere, it's slop

**Validation:**
- Validation at system boundaries (API endpoints, user input) is good
- Validation of internal function calls is usually slop
- Check existing patterns in the file

**Comments:**
- Complex algorithm explanations are valuable
- "Why" comments explaining non-obvious decisions are valuable
- "What" comments describing obvious code are slop

## Process

### 0. Validation Before Removing

For each potential slop pattern:
1. Verify it appears in 3+ places in the diff
2. Check if it's inconsistent with original file style
3. Confirm it's not fixing a real bug or adding necessary functionality

Only remove patterns that clearly don't match the codebase conventions.

### 1. Get the Full Diff

```bash
git diff main...HEAD
```

If the diff is large, review files individually:
```bash
git diff main...HEAD --name-only | head -20
```

### 2. Review Each Changed File

For each file in the diff:

**Check the original file context:**
- Read surrounding code to understand existing style
- Look for patterns: error handling, comments, validation
- Note the file's conventions

**Identify slop patterns:**
- Compare new code to existing code style
- Flag inconsistencies with the file's patterns
- Mark defensive code not present elsewhere

**Preserve legitimate changes:**
- Don't remove actual functionality
- Keep necessary type fixes
- Keep valuable comments and error handling

### 3. Remove Slop

Remove identified patterns while preserving the actual changes.

**Use Edit tool** to remove slop:
- Remove excessive comments
- Remove unnecessary defensive checks
- Fix type workarounds properly instead of casting to `any`
- Move inline imports to top of file
- Align style with rest of file

### 4. Report Changes

Provide a brief summary (1-3 sentences) of what was removed.

## Examples

### Excessive Comments - Remove

```python
# Bad - AI slop
def calculate_total(items):
    # Initialize the total to zero
    total = 0
    # Loop through each item in the items list
    for item in items:
        # Add the item price to the running total
        total += item.price
    # Return the final calculated total
    return total

# Good - clean
def calculate_total(items):
    total = 0
    for item in items:
        total += item.price
    return total
```

### Unnecessary Defensive Checks - Usually Remove

```typescript
// Bad - AI slop (if user is guaranteed non-null from auth middleware)
function getUserProfile(user: User) {
  if (!user) {
    throw new Error("User is required");
  }
  if (!user.id) {
    throw new Error("User ID is required");
  }
  return db.query("SELECT * FROM profiles WHERE user_id = ?", [user.id]);
}

// Good - clean (trust the type system and middleware)
function getUserProfile(user: User) {
  return db.query("SELECT * FROM profiles WHERE user_id = ?", [user.id]);
}
```

### Type Casts - Remove and Fix Properly

```typescript
// Bad - AI slop
const data = JSON.parse(response) as any;
const userId = (data as any).user.id;

// Good - fix the types properly
interface ApiResponse {
  user: {
    id: string;
  };
}
const data = JSON.parse(response) as ApiResponse;
const userId = data.user.id;
```

### Inline Imports - Move to Top

```python
# Bad - AI slop
def process_data(data):
    from datetime import datetime  # Don't do this
    return datetime.now()

# Good - clean
from datetime import datetime

def process_data(data):
    return datetime.now()
```

### Legitimate Comments - Keep

```python
# Good - explains non-obvious "why"
def calculate_discount(price, user):
    # Apply 10% discount for beta users to encourage adoption during testing phase
    if user.is_beta:
        return price * 0.9
    return price
```

### Legitimate Error Handling - Keep

```typescript
// Good - API boundary needs error handling
async function fetchUserData(userId: string) {
  try {
    return await api.getUser(userId);
  } catch (error) {
    logger.error("Failed to fetch user", { userId, error });
    throw new UserFetchError("Unable to retrieve user data");
  }
}
```

## Determining if Code is Slop

**Ask these questions:**

1. **Does the rest of the file do this?**
   - If no similar code has try/catch, it's probably slop
   - If no functions have docstrings, added docstrings are slop

2. **Is this pattern consistent with the codebase?**
   - Check other files in the same directory
   - Look for similar functions elsewhere

3. **Is this defensive check necessary?**
   - Is the data already validated?
   - Is the caller trusted internal code?
   - Does the type system already guarantee this?

4. **Does this comment add value?**
   - Does it explain "why" rather than "what"?
   - Would a human write this comment?
   - Is it consistent with other comments in the file?

**When in doubt:**
- Read more of the existing codebase
- Err on the side of keeping legitimate improvements
- If the whole codebase lacks error handling, don't remove it all at once

## Tool Usage

- **Use `git diff main...HEAD`** to see all changes
- **Use Read tool** to examine full file context
- **Use Grep tool** to search for similar patterns in the codebase
- **Use Edit tool** to remove identified slop
- **Use Task tool with Explore agent** to understand codebase patterns

## Common Mistakes

**Don't remove too much:**
- Legitimate error handling at boundaries
- Valuable "why" comments
- Necessary type annotations

**Don't remove too little:**
- Be aggressive with obvious slop
- Comments describing what the code does
- Defensive checks on trusted internal data

**Do preserve functionality:**
- Remove slop, not features
- Keep the actual logic changes
- Don't break working code

## Output

Report what was removed (1-3 sentences):

```
Removed excessive comments from user.py and auth.py that described obvious
code flow. Removed unnecessary null checks in getProfile() since user is
guaranteed non-null from auth middleware. Fixed type cast to 'any' in
api.ts by adding proper interface.
```

Be specific but concise. Focus on patterns removed, not every individual change.
