---
name: code-review
description: "Review all changes on the current branch vs main (or a custom base) for bugs, security issues, type safety, DRY violations, and project conventions. Use when the user says /code-review, asks to review the branch, wants a code review before opening a PR, or wants to check if changes are ready to merge."
---

# Code Review

## Step 1: Gather context

```bash
git branch --show-current
git log <base>..HEAD --oneline              # commits on this branch
git diff <base>...HEAD --stat               # files changed
gh pr view --json number,title,url,body 2>/dev/null || echo "No PR yet"
```

Default base branch: `main`. If the user passed a different branch as argument, use that.

If a PR exists, also fetch any existing review comments:
```bash
gh api repos/{owner}/{repo}/pulls/{number}/reviews
```

---

## Step 2: Review every changed file

Get the full diff: `git diff <base>...HEAD -- <file>` for each changed file.

For each file, look for:

**Logic & correctness**
- Bugs, edge cases not handled, off-by-one errors, race conditions
- Null/undefined not checked where it could blow up

**Type safety** *(TypeScript/typed languages)*
- Missing types, unsafe casts, `any` that shouldn't be there

**Security**
- Input not validated or sanitized
- SQL/command injection vectors
- XSS risks, sensitive data in logs or responses
- Hardcoded secrets or tokens

**Error handling**
- Missing try/catch on async operations
- Unhandled promise rejections
- Errors swallowed silently

**Code quality**
- Dead code, unnecessary complexity, unclear naming
- Magic numbers/strings that should be constants

**DRY violations**
- Duplicated types, interfaces, or logic that should be extracted

**Debug artifacts**
- `console.log`, `console.error`, `debugger`, commented-out code left in
- TODO/FIXME comments that should be resolved before merging

**Project conventions**
- Does it follow the patterns established in this codebase (CLAUDE.md, existing code structure)?
- Consistent with how similar things are done elsewhere?

---

## Step 3: Typecheck (if TypeScript changes)

```bash
pnpm run typecheck   # or npm run typecheck / tsc --noEmit
```

Report any new type errors introduced by this branch.

---

## Step 4: Output

```markdown
## Code Review

**PR #X: Title** (or "Branch: `name`" if no PR yet)

### Overall Assessment
[Good to merge / Needs fixes before PR / Needs fixes before merge]

### Must Fix (blocking)
#### 1. Issue title
**File:** `path/to/file.ts:line`
Description of the problem and suggested fix.

### Should Fix (non-blocking)
- `file.ts:line` — minor issue description

### Good Practices Observed
- Things done well worth noting

### Verdict
[Final recommendation: ready to merge / needs fixes. List what needs to be addressed if any.]
```

---

## Severity rules (non-negotiable)

**Must fix before merge:**
- Debug statements left in production code
- DRY violations (duplicated types, repeated logic)
- Missing error handling for user-facing operations
- Any security issue
- Logic errors / bugs

**Should fix (can merge, follow up):**
- Minor naming inconsistencies
- Missing optional optimizations
- Documentation gaps

Do NOT downplay must-fix issues as "nice-to-have". Debug artifacts and DRY violations are blocking — they indicate incomplete work.
